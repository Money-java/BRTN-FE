<template>
    <div class="container mt-5">
        <div class="row">
            <!-- 사이드바 버튼들 -->
            <div class="col-2 sidebar">
                <div class="d-flex flex-column">
                    <button type="button" class="btn btn-dark mb-3" data-bs-toggle="modal" data-bs-target="#myModal">
                        루틴 추가하기
                    </button>
                    <button type="button" class="btn btn-dark mb-3" @click="updateRoutineState">
                        상태 업데이트
                    </button>
                </div>
            </div>

            <!-- 대기 및 진행 중 리스트 -->
            <div class="col-10">
                <div class="row">
                    <!-- 대기 리스트 -->
                    <div class="col-6">
                        <h4 class="d-flex align-items-center">
                            <i class="fa fa-circle px-2" style="font-size:16px; color:red;" aria-hidden="true"></i>
                            대기
                        </h4>
                        <draggable class="dragArea list-group" :list="waitingList" group="tasks" item-key="myHabitId"
                            @start="handleDragStart" @end="onDragEnd('inProgress')">
                            <template #item="{ element }">
                                <div class="list-group-item d-flex justify-content-between align-items-center">
                                    <div class="d-flex align-items-center">
                                        <span class="category-badge" :class="getCategoryClass(element.categoryTitle)">
                                            {{ element.categoryTitle }}
                                        </span>
                                        <span class="task-name mx-3">{{ element.habitTitle }}</span>
                                    </div>
                                    <div class="d-flex bd-highlight">
                                        <button class="btn" data-bs-toggle="modal" data-bs-target="#editModal"
                                            @click="confirmEdit('waiting', element)">
                                            <i class="fa fa-pencil text-primary" aria-hidden="true"></i>
                                        </button>
                                        <button class="btn" data-bs-toggle="modal" data-bs-target="#removeModal"
                                            @click="confirmRemove('waiting', element.myHabitId, element.habitTitle)">
                                            <i class="fa fa-minus-circle text-danger" aria-hidden="true"></i>
                                        </button>
                                    </div>
                                </div>
                            </template>
                        </draggable>
                    </div>

                    <!-- 진행 중 리스트 -->
                    <div class="col-6">
                        <h4 class="d-flex align-items-center">
                            <i class="fa fa-circle px-2" style="font-size:16px; color:green;" aria-hidden="true"></i>
                            진행중
                        </h4>
                        <draggable class="dragArea list-group" :list="inProgressList" group="tasks" item-key="myHabitId"
                            @change="checkListLength" @end="onDragEnd('waiting')">
                            <template #item="{ element }">
                                <div class="list-group-item d-flex justify-content-between align-items-center">
                                    <div class="d-flex align-items-center">
                                        <span class="category-badge" :class="getCategoryClass(element.categoryTitle)">
                                            {{ element.categoryTitle }}
                                        </span>
                                        <span class="task-name mx-3">{{ element.habitTitle }}</span>
                                    </div>
                                    <div class="d-flex bd-highlight">
                                        <button class="btn" @click="confirmEdit('inProgress', element)">
                                            <i class="fa fa-pencil text-primary" aria-hidden="true"></i>
                                        </button>
                                        <button class="btn" data-bs-toggle="modal" data-bs-target="#removeModal"
                                            @click="confirmRemove('inProgress', element.myHabitId, element.habitTitle)">
                                            <i class="fa fa-minus-circle text-danger" aria-hidden="true"></i>
                                        </button>
                                    </div>
                                </div>
                            </template>
                        </draggable>
                    </div>
                </div>

                <!-- 완료된 리스트 아래로 배치 -->
                <div class="row mt-5">
                    <div class="col-12">
                        <h4 class="d-flex align-items-center">
                            <i class="fa fa-circle px-2" style="font-size:16px; color:blue;" aria-hidden="true"></i>
                            완료됨
                        </h4>
                        <draggable class="dragArea list-group" :list="completedList" group="tasks" item-key="myHabitId">
                            <template #item="{ element }">
                                <div class="list-group-item d-flex justify-content-between align-items-center">
                                    <div class="d-flex align-items-center">
                                        <span class="category-badge" :class="getCategoryClass(element.categoryTitle)">
                                            {{ element.categoryTitle }}
                                        </span>
                                        <span class="task-name mx-3">{{ element.habitTitle }}</span>
                                    </div>
                                </div>
                            </template>
                        </draggable>
                    </div>
                </div>
            </div>
        </div>
    </div>


    <!-- 루틴 추가 Modal -->
    <div class="modal fade" id="myModal">
        <div class="modal-dialog modal-dialog-centered">
            <div class="modal-content">

                <!-- Modal Header -->
                <div class="modal-header">
                    <h4 class="modal-title">루틴 추가하기</h4>
                    <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                </div>

                <!-- Modal body -->
                <div class="modal-body">
                    <label>카테고리 선택</label>
                    <select v-model="newHabitCategory" class="form-select" aria-label="Category select"
                        id="categorySelect">
                        <option disabled value="">카테고리를 선택하세요</option>
                        <option v-for="category in categories" :key="category.value" :value="category.value">
                            {{ category.label }}
                        </option>
                    </select>
                    <label>이름</label>
                    <input v-model="newHabitName" type="text" class="form-control" placeholder="이름 입력">
                    <label>금액</label>
                    <input v-model="newHabitSaveAmount" type="text" class="form-control" placeholder="금액 입력">
                    <label>달성 조건</label>
                    <input v-model="newHabitCertification" type="text" class="form-control" placeholder="달성 조건 입력">
                </div>

                <!-- Modal footer -->
                <div class="modal-footer">
                    <button class="btn" :class="isAddFormValid ? 'btn-dark' : 'btn-secondary'" @click="addHabit"
                        :disabled="!isAddFormValid">
                        추가하기
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- 루틴 수정 Modal -->
    <div class="modal fade" id="editModal">
        <div class="modal-dialog modal-dialog-centered">
            <div class="modal-content">

                <!-- Modal Header -->
                <div class="modal-header">
                    <h4 class="modal-title">루틴 수정하기</h4>
                    <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                </div>

                <!-- Modal body -->
                <div class="modal-body">
                    <label>카테고리 선택</label>
                    <select v-model="editHabitCategory" class="form-select" aria-label="Category select"
                        id="categorySelect">
                        <option disabled value="">카테고리를 선택하세요</option>
                        <option v-for="category in categories" :key="category.value" :value="category.value">
                            {{ category.label }}
                        </option>
                    </select>
                    <label>이름</label>
                    <input v-model="editHabitName" type="text" class="form-control" placeholder="이름 입력">
                    <label>금액</label>
                    <input v-model="editHabitSaveAmount" type="text" class="form-control" placeholder="금액 입력">
                    <label>달성 조건</label>
                    <input v-model="editHabitCertification" type="text" class="form-control" placeholder="달성 조건 입력">
                </div>

                <!-- Modal footer -->
                <div class="modal-footer">
                    <button class="btn" data-bs-dismiss="modal" :class="isEditFormValid ? 'btn-dark' : 'btn-secondary'"
                        @click="editItem" :disabled="!isEditFormValid">
                        수정하기
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- 삭제 확인 모달 -->
    <div class="modal fade" id="removeModal">
        <div class="modal-dialog modal-dialog-centered">
            <div class="modal-content">

                <!-- Modal Header -->
                <div class="modal-header">
                    <h4 class="modal-title">루틴 삭제하기</h4>
                    <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
                </div>

                <!-- Modal body -->
                <div class="modal-body">
                    "{{ deleteHabitTitle }}" 루틴을 삭제하시겠습니까?
                </div>

                <!-- Modal footer -->
                <div class="modal-footer">
                    <button class="btn btn-secondary" data-bs-dismiss="modal">취소</button>
                    <button class="btn btn-danger" @click="removeItem" data-bs-dismiss="modal">삭제</button>
                </div>
            </div>
        </div>
    </div>

    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
</template>

<script>
import { ref, computed, onMounted } from 'vue';
import draggable from "vuedraggable";
import axios from 'axios';

export default {
    name: "TaskManager",
    components: {
        draggable
    },
    setup() {
        // 상태
        const newHabitCategory = ref("");
        const newHabitName = ref("");
        const newHabitSaveAmount = ref("");
        const newHabitCertification = ref("");
        const editHabitId = ref("");
        const editMyHabitId = ref("");
        const editHabitName = ref("");
        const editHabitCategory = ref("");
        const editHabitSaveAmount = ref("");
        const editHabitCertification = ref("");
        const deleteListType = ref("");
        const deleteMyHabitId = ref("");
        const deleteHabitTitle = ref("");
        const waitingList = ref([]);
        const inProgressList = ref([]);

        const categories = ref([
            { value: "식비", label: "식비 (Food)" },
            { value: "카페/간식", label: "카페/간식 (Cafe/Snacks)" },
            { value: "온라인쇼핑", label: "온라인쇼핑 (Online Shopping)" },
            { value: "패션/쇼핑", label: "패션/쇼핑 (Fashion/Shopping)" },
            { value: "문화/여가", label: "문화/여가 (Culture/Leisure)" },
            { value: "술/유흥", label: "술/유흥 (Alcohol/Entertainment)" },
            { value: "교육", label: "교육 (Education)" },
            { value: "의료/건강", label: "의료/건강 (Medical/Health)" },
            { value: "생활", label: "생활 (Living)" },
            { value: "주거/공과금", label: "주거/공과금 (Housing/Utilities)" },
            { value: "금융", label: "금융 (Finance)" },
            { value: "뷰티", label: "뷰티 (Beauty)" },
            { value: "자동차", label: "자동차 (Automobile)" },
            { value: "교통", label: "교통 (Transportation)" },
            { value: "반려동물", label: "반려동물 (Pets)" },
            { value: "여행", label: "여행 (Travel)" },
            { value: "경조사/회비", label: "경조사/회비 (Events/Fees)" }
        ]);

        // Computed
        const isAddFormValid = computed(() => newHabitCategory.value && newHabitName.value);
        const isEditFormValid = computed(() => editHabitCategory.value && editHabitName.value);

        // 메서드
        const handleDragStart = (evt) => {
            console.log("Drag started:", evt);
        };

        const checkListLength = () => {
            if (inProgressList.value.length >= 5) {
                alert("진행 목록에는 최대 5개의 항목만 추가할 수 있습니다.");
                return false; // 이동을 막음
            }
        };
        const onDragEnd = (listType) => {
            console.log("Drag Ended for:", listType);
            try {
                if (listType === 'waiting') {
                    waitingList.value.forEach(habit => {
                        habit.state = '대기';
                    });
                } else if (listType === 'inProgress') {
                    // inProgressList에 5개 이상이면 더 이상 추가하지 않음
                    if (inProgressList.value.length >= 5) {
                        alert("진행 목록에는 최대 5개의 항목만 추가할 수 있습니다.");
                        return;
                    }
                    else {
                        inProgressList.value.forEach(habit => {
                            habit.state = '진행';
                        });
                    }
                }
            } catch (error) {
                console.error("드래그 후 상태 업데이트 중 오류 발생:", error);
            }
        };

        const getCategoryClass = (category) => {
            const classes = {
                "식비": "badge-food",
                "카페/간식": "badge-snack",
                "온라인쇼핑": "badge-online-shopping",
                "패션/쇼핑": "badge-fashion",
                "문화/여가": "badge-culture",
                "술/유흥": "badge-alcohol",
                "교육": "badge-education",
                "의료/건강": "badge-health",
                "생활": "badge-lifestyle",
                "주거/공과금": "badge-housing",
                "금융": "badge-finance",
                "뷰티": "badge-beauty",
                "자동차": "badge-automobile",
                "교통": "badge-transport",
                "반려동물": "badge-pet",
                "여행": "badge-travel",
                "경조사/회비": "badge-events",
            };
            return classes[category] || "badge-default";
        };

        const getHabitList = async () => {
            try {
                const response = await axios.get('http://localhost:8080/habits/', {
                    params: {
                        userId: localStorage.getItem("userId")
                    }
                });
                waitingList.value = [];
                inProgressList.value = [];

                response.data.forEach(habit => {
                    if (habit.state === "대기") {
                        waitingList.value.push(habit);
                    } else if (habit.state === "진행") {
                        inProgressList.value.push(habit);
                    }
                });

                console.log("대기 리스트:", waitingList.value);
                console.log("진행중 리스트:", inProgressList.value);

            } catch (error) {
                console.error("습관 가져오기 중 오류 발생:", error);
                alert("습관 가져오기 중 오류가 발생했습니다.");
            }
        };

        const addHabit = async () => {
            try {
                const request = {
                    userId: localStorage.getItem("userId"),
                    habitTitle: newHabitName.value,
                    categoryTitle: newHabitCategory.value,
                    saveAmount: newHabitSaveAmount.value,
                    certification: newHabitCertification.value,
                    state: "대기"
                };

                const response = await axios.post('http://localhost:8080/habits/add/my', request, {
                    headers: { 'Content-Type': 'application/json' }
                });
                if (response.data === 'duplicate') {
                    alert("이미 같은 이름의 루틴이 있습니다!");
                    return;
                }
                alert("루틴이 성공적으로 추가되었습니다!");

                waitingList.value.push({
                    myHabitId: response.data.myHabitId,
                    habitId: response.data.habitId,
                    habitTitle: request.habitTitle,
                    userId: localStorage.getItem("userId"),
                    state: "대기",
                    categoryTitle: request.categoryTitle
                });

                newHabitCategory.value = "";
                newHabitName.value = "";
                newHabitSaveAmount.value = "";
                newHabitCertification.value = "";

            } catch (error) {
                console.error("습관 추가 중 오류 발생:", error);
                alert("습관 추가 중 오류가 발생했습니다.");

            }
        };

        const confirmEdit = (type, element) => {
            if (type === 'inProgress') {
                alert('진행중인 루틴은 수정할 수 없습니다!');
            }
            else {
                editMyHabitId.value = element.myHabitId;
                editHabitId.value = element.habitId;
                editHabitName.value = element.habitTitle;
                editHabitCategory.value = element.categoryTitle;
                editHabitSaveAmount.value = element.saveAmount;
                editHabitCertification.value = element.certification;
            }
        };

        const editItem = async () => {
            const request = {
                myHabitId: editHabitId.value,
                habitId: editHabitId.value,
                userId: localStorage.getItem("userId"),
                habitTitle: editHabitName.value,
                categoryTitle: editHabitCategory.value,
                saveAmount: editHabitSaveAmount.value,
                certification: editHabitCertification.value
            }
            try {
                const response = await axios.put("http://localhost:8080/habits/update", request, {
                    headers: { 'Content-Type': 'application/json' }
                });
                alert("결과: " + response.data);
            }
            catch (error) {
                console.error("루틴 수정 중 오류 발생:", error);
            }

        };

        const updateRoutineState = async () => {
            try {
                const combinedList = [...waitingList.value, ...inProgressList.value];
                const userId = localStorage.getItem("userId");

                const response = await axios.put("http://localhost:8080/habits/update/state", combinedList, {
                    params: { userId: userId },
                    headers: { 'Content-Type': 'application/json' }
                });
                alert("루틴 상태가 성공적으로 업데이트되었습니다!");

            } catch (error) {
                console.error("루틴 상태 업데이트 중 오류 발생:", error);
                alert("루틴 상태 업데이트 중 오류가 발생했습니다.");
            }
        };

        const confirmRemove = (listType, myHabitId, habitTitle) => {
            deleteListType.value = listType;
            deleteMyHabitId.value = myHabitId;
            deleteHabitTitle.value = habitTitle;
        };

        const removeItem = async () => {
            try {
                await axios.delete("http://localhost:8080/habits/delete", {
                    params: { myHabitId: deleteMyHabitId.value }
                });

                if (deleteListType.value === "waiting") {
                    waitingList.value = waitingList.value.filter(item => item.myHabitId !== deleteMyHabitId.value);
                } else if (deleteListType.value === "inProgress") {
                    inProgressList.value = inProgressList.value.filter(item => item.myHabitId !== deleteMyHabitId.value);
                }

                alert("루틴이 성공적으로 삭제되었습니다!");
            } catch (error) {
                console.error("루틴 삭제 중 오류 발생:", error);
                alert("루틴 삭제 중 오류가 발생했습니다.");
            }

            deleteListType.value = "";
            deleteMyHabitId.value = "";
            deleteHabitTitle.value = "";
        };

        // LifeCycle Hook
        onMounted(() => {
            getHabitList();
        });

        return {
            newHabitCategory,
            newHabitName,
            newHabitSaveAmount,
            newHabitCertification,
            editHabitCategory,
            editHabitName,
            editMyHabitId,
            editHabitId,
            editHabitCertification,
            editHabitSaveAmount,
            deleteListType,
            deleteMyHabitId,
            deleteHabitTitle,
            categories,
            waitingList,
            inProgressList,
            isAddFormValid,
            isEditFormValid,
            handleDragStart,
            onDragEnd,
            getCategoryClass,
            getHabitList,
            addHabit,
            updateRoutineState,
            confirmEdit,
            editItem,
            confirmRemove,
            removeItem,
            checkListLength,
        }
    }
}
</script>


<style scoped>
.dragArea {
    min-height: 150px;
    border: 1px solid #ccc;
    border-radius: 8px;
    padding: 10px;
}

.list-group-item {
    cursor: grab;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.list-group-item:active {
    cursor: grabbing;
}

/* Category badge styling */
.category-badge {
    padding: 5px 10px;
    border-radius: 12px;
    color: white;
    font-size: 0.75rem;
    width: 70px;
    text-align: center;
    text-overflow: ellipsis;
}

/* Category specific styles */
.badge-food {
    background-color: #5193F4;
}

.badge-snack {
    background-color: #8B6555;
}

.badge-online-shopping {
    background-color: #3EBAC2;
}

.badge-fashion {
    background-color: #F5778D;
}

.badge-culture {
    background-color: #8C46C2;
}

.badge-alcohol {
    background-color: #FFAD0D;
}

.badge-education {
    background-color: #A2C616;
}

.badge-health {
    background-color: #FA4949;
}

.badge-lifestyle {
    background-color: #5B9FF4;
}

.badge-housing {
    background-color: #6D5D3F;
}

.badge-finance {
    background-color: #757E95;
}

.badge-beauty {
    background-color: #E659E9;
}

.badge-automobile {
    background-color: #5B9FF4;
}

.badge-transport {
    background-color: #949BAD;
}

.badge-pet {
    background-color: #6D5D3F;
}

.badge-travel {
    background-color: #696E76;
}

.badge-events {
    background-color: #F39C77;
}

.badge-default {
    background-color: #ffdfba;
    color: #d08b44;
}

/* 루틴 명 스타일 */
.task-name {
    font-weight: bold;
    font-size: 1rem;
}
</style>
