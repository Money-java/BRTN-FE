<template>
    <!-- 루틴 커뮤니티 -->
 <div class="intro">
  <div class="introLeft">
    <h3>Routine Share Community</h3>
    <p>
      나만의 '벌루틴'을 공유하고, 다른 사람들의 '벌루틴'을 내것으로 만들어보세요!
    </p>
  </div>
  
  <!-- 검색창 -->
  <div class="search-routine">
    <input type="text" v-model="displayedQuery" placeholder="검색어를 입력하세요" />
    <button @click="performSearch">검색</button>
  </div>
</div>
   

  <nav class="filter">
    <div class="categoryFilter">
      <form action="#">
        <label for="category_filter">카테고리</label>
        <select
          name="category"
          id="category"
          @change="handleCategoryFilterChange"
        >
        <option value="all">전체</option>
          <option value="food">식비</option>
          <option value="dessert">카페/간식</option>
          <option value="online_shopping">온라인쇼핑</option>
          <option value="fashion_shopping">패션/쇼핑</option>
          <option value="culture_leisure">문화/여가</option>
          <option value="alcohol_entertainment">술/유흥</option>
          <option value="education">교육</option>
          <option value="health_medical">의료/건강</option>
          <option value="living">생활</option>
          <option value="housing_utilities">주거/공과금</option>
          <option value="travel">여행</option>
          <option value="automobile">자동차</option>
          <option value="pet">반려동물</option>
          <option value="beauty">뷰티</option>
          <option value="finance">금융</option>
          <option value="transportation">교통</option>
          <option value="event_fees">경조사/회비</option>

        </select>
      </form>
    </div>

    <div class="otherFilter">
      <form action="#">
        <input
          type="radio"
          name="otherFilter"
          value="recent"
          v-model="currentSortType"
          @change="handleFilterChange"
        />
        최신 순
        <input
          type="radio"
          name="otherFilter"
          value="manyLikes"
          v-model="currentSortType"
          @change="handleFilterChange"
        />
        좋아요 순
        <input
          type="radio"
          name="otherFilter"
          value="manyParticipants"
          v-model="currentSortType"
          @change="handleFilterChange"
        />
        참여자 순
        <input
          type="radio"
          name="otherFilter"
          value="manyCompletions"
          v-model="currentSortType"
          @change="handleFilterChange"
        />
        달성자 순
        <input
          type="radio"
          name="otherFilter"
          value="myLikes"
          v-model="currentSortType"
          @change="handleFilterChange"
        />
        나의 좋아요
      </form>
    </div>

    <!-- <input type="submit" value="Submit" /> -->
  </nav>

  <div class="main">
    <!-- 인증사진 프리뷰 -->
    <div class="shots">
        <h5>SHOT PREVIEW</h5>
        <p>벌루틴을 클릭하면, 해당 루틴에 대한 사람들의 꿀샷을 미리볼 수 있어요.</p>
        <!-- 선택한 루틴 제목 표시 -->
        <!-- <h4 v-if="selectedHabitTitle">{{ selectedHabitTitle }}</h4> -->

        <div class="row gx-0 gy-0"> <!-- 열과 행 간의 간격을 없앰 -->
        <div class="col-6 col-md-6" v-for="shot in previewPosts" :key="shot.post_id" @click="logImageUrl(shot.imageUrl)">
            <div class="card h-100"> <!-- 카드 높이를 100%로 설정 -->
                <img :src="shot.imageUrl" class="card-img-top img-fluid">                
            </div>
        </div>
    </div>
    </div>

    <!-- 카드들 -->
    <!-- <div class="cards row row-cols-1 row-cols-md-3 g-4" v-if="(isMyLikesFilterActive ? checkMyLike() : routineCommunityArray).length > 0"> -->
    <!-- 좋아요 누른 게 없을 때 문구 띄우기 -->
    <div class="cards row row-cols-1 row-cols-md-3 g-4">
      <!-- 카드 하나 -->
      <!-- v-for 문 !!! -->
      <!-- <div class="col" v-for="routine in routineCommunityArray" :key="routine.community_id"> -->
      <div
        class="col"
        v-for="routine in translatedRoutines"
        :key="routine.communityId"
      >
        <!-- <div class="card h-100 "> -->
        <div class="card h-100" @click="selectHabit(routine.habitId)">
          <div class="card-body">
            <div class="subtitle">
              <span class="type card-subtitle">
                {{ routine.categoryTitle }}
              </span>
              <span class="date card-subtitle">
                {{ routine.uploadDate }}
              </span>
              <!-- mb-2 text-body-secondary -->
            </div>
            <h5 class="card-title">{{ routine.habitTitle }}</h5>
          <div class="card-text">
            <div class="writer">
                <!-- <img class="avatar" src="@/assets/images/sample.jpg"> -->
                <img class="avatar" :src="routine.avatar || defaultAvatar" />
                <span class="writerName">{{ routine.nickname }}</span>
            </div>
            <div class="likeContainer">
                <button class="likeButton" @click="toggleLike(routine.communityId)">
                  <img
                    class="likeImg"
                    :src="isLiked(routine.communityId) ? fullLike : emptyLike"
                    alt="like"
                  />
                </button>
                <span class="likeComment">{{ routine.habitLikes }}</span>
              </div>
            </div>

            <div class="challengeContainer">
              <p class="challengeComment">
                {{ routine.participants }}명의 루티너 중<br />
                오늘 {{ routine.complete }}명이 목표를 달성했어요.
              </p>
              <!-- <a href="#" class="challengeButton btn btn-primary">
                <img
                  class="challengeIcon"
                  src="@/assets/icons/together_invertColor.png"
                />
                <span class="challengeButtonText">내 루틴에 담고 함께 도전하기</span>
              </a> -->
              <button
                @click="addHabitToMyHabit(routine.communityId)"
                class="challengeButton btn btn-primary"
              >
                <img
                  class="challengeIcon"
                  src="@/assets/icons/together_invertColor.png"
                />
                <span class="challengeButtonText">내 루틴에 담고 함께 도전하기</span>
              </button>
            </div>
          </div>
        </div>
      </div>
      <!-- 카드 하나 끝-->
      <!-- 카드 끝 -->
    </div>
    <!-- 좋아요한 루틴이 없을 때 표시할 메시지 -->
    <!-- <p v-else>좋아요한 루틴이 없습니다.</p>  -->
  </div>
  <!-- 페이지네이션 컴포넌트 -->
   
  <paginate
    :page-count="totalPages"
    :click-handler="changePage"
    :prev-text="'Prev'"
    :next-text="'Next'"
    :container-class="'pagination'"
    :page-class="'page-item'"
    :page-link-class="'page-link'"
    :prev-link-class="'page-link-prev'"
    :next-link-class="'page-link-next'"
    :active-class="'active'"
/>


</template>

<script setup>
import { ref, onMounted, watch ,computed} from 'vue';
import axios from 'axios';
import fullLike from '@/assets/icons/fullLike.png';
import emptyLike from '@/assets/icons/emptyLike.png';
import coffeeImg from '@/assets/images/coffee_sample.png';
import coffeeImg2 from '@/assets/images/coffee_sample2.jpg';
import foodImg from '@/assets/images/food_sample.jpg';
import foodImg2 from '@/assets/images/food_sample2.jpg';
import defaultAvatarImg from '@/assets/images/sample.jpg';
import Paginate from 'vuejs-paginate-next';

const defaultAvatar = defaultAvatarImg;

// props로 sortType 받기
const props = defineProps({
  sortType: {
    type: String,
    default: 'recent'  // 기본값으로 'recent'를 사용
  }
});

// **카테고리명을 한국어로 변환하는 매핑 테이블**  <!-- 추가된 부분 -->
const categoryTranslations = {
  food: '식비',
  dessert: '카페/간식',
  online_shopping: '온라인쇼핑',
  fashion_shopping: '패션/쇼핑',
  culture_leisure: '문화/여가',
  alcohol_entertainment: '술/유흥',
  education: '교육',
  health_medical: '의료/건강',
  living: '생활',
  housing_utilities: '주거/공과금',
  travel: '여행',
  automobile: '자동차',
  pet: '반려동물',
  beauty: '뷰티',
  finance: '금융',
  transportation: '교통',
  event_fees: '경조사/회비'
};

// sortType을 상태로 관리할 변수 선언
const currentSortType = ref(props.sortType); // props.sortType을 상태로 복사해서 관리
const routineCommunityArray = ref([]);
const selectedCategory = ref('all');  // 카테고리 필터
const likesArray = ref([]);

// 카테고리 타이틀을 한국어로 변환하는 함수
const translatedRoutines = computed(() => { 
  return routineCommunityArray.value.map(routine => ({
    ...routine,
    categoryTitle: categoryTranslations[routine.categoryTitle] || routine.categoryTitle 
  }));  // map 함수의 닫는 괄호 위치 수정
});

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
// SHOT PREVIEW
const previewPosts = ref([]); // 인증 사진을 저장할 변수

// 페이지 로드시 SHOT PREVIEW에 1번째, 2번째, 3번째 루틴의 인증 사진을 표시하는 함수
const fetchPreviewPosts = async (habitIds) => {
  try {
    const promises = habitIds.map(habitId => axios.get(`http://localhost:8080/routine-community/posts/${habitId}`));
    const responses = await Promise.all(promises);
    const posts = responses.flatMap(response => response.data);
    
    // 중복되지 않게 최대 8개의 사진만 저장
   previewPosts.value = posts.map(post => ({
      post_id: post.postId,
      habit_id: post.habitId,
      imageUrl: post.imageURL ? post.imageURL : post.imageUrl // imageURL을 imageUrl로 일관성 있게 처리
    })).slice(0, 8);
    console.log('Fetched initial preview posts:', previewPosts.value);
  } catch (error) {
    console.error('Error fetching initial preview posts:', error);
  }
};


const currentPage = ref(1);  // 현재 페이지 번호
const totalPages = ref(1);  // 총 페이지 수
const pageSize = ref(12);  // 한 페이지에 표시할 데이터 수

const changePage = (page) => {
    currentPage.value = page;
    fetchRoutines();  // 페이지 변경 시 루틴 목록 다시 가져오기
};

// 루틴 데이터를 가져오는 함수 (검색, 정렬, 필터를 모두 처리)
const fetchRoutines = async (keyword = '') => {
  try {
    // const authStore = useAuthStore()
    // const userId = authStore.userId // Pinia에서 userId 가져오기
    const userId = localStorage.getItem('userId');

    const params = {
      keyword: keyword || null,
      categoryName: selectedCategory.value !== 'all' ? selectedCategory.value : null,
      sortType: currentSortType.value || 'recent',
      page: currentPage.value,
      size: pageSize.value,
    }

    // '나의 좋아요' 필터가 선택된 경우 userId 추가
    if (currentSortType.value === 'myLikes' && userId) {
      params.userId = userId
    }

    const response = await axios.get('http://localhost:8080/routine-community/search-or-sort', {
      params: params,
    })

    routineCommunityArray.value = response.data.communities;
    totalPages.value = response.data.totalPages;  // 서버에서 총 페이지 수를 받아와서 설정

    // 페이지 로드시 첫 번째, 두 번째, 세 번째 루틴의 habit_id를 이용해 인증 사진을 가져옴
    if (routineCommunityArray.value.length >= 3) {
      const habitIds = [
        routineCommunityArray.value[0].habitId,
        routineCommunityArray.value[1].habitId,
        routineCommunityArray.value[2].habitId,
      ]
      fetchPreviewPosts(habitIds) // 인증 사진을 미리 가져오는 함수 호출
    }
  } catch (error) {
    console.error('루틴 데이터 가져오는 중 오류 발생:', error)
  }
};

// 페이지 변경 함수
// const changePage = (newPage) => {
//   if (newPage >= 1 && newPage <= totalPages.value) {
//     currentPage.value = newPage;
//     fetchRoutines();  // 페이지 변경 시 데이터를 다시 로드
//   }
// };

const searchQuery = ref('');  // 검색어를 저장할 변수
const displayedQuery = ref('');  // 화면에 보여질 검색어 상태

const performSearch = () => {
  console.log('Search button clicked. Keyword:', searchQuery.value);
  searchQuery.value = displayedQuery.value;  // 실제 검색어는 유지
  fetchRoutines(searchQuery.value);  // 검색어를 기반으로 데이터를 가져옴
  displayedQuery.value = "";  // 화면에 표시되는 검색어는 비움
};


// 필터가 변경되면 sortType 업데이트 후 데이터 요청
// 오른쪽 필터 (ex: 최신순, 좋아요 순, 참여자 순, 달성자 순, 나의 좋아요)
function handleFilterChange(event) {
  currentSortType.value = event.target.value;  // 읽기 전용인 props.sortType 대신 상태로 관리되는 currentSortType을 변경
  console.log("Selected sortType: ", currentSortType.value);
  fetchRoutines(searchQuery.value);  // 업데이트된 sortType으로 데이터를 가져옴
}

// 왼쪽 카테고리 필터 (ex: 식비, 여행, 주거/공과금.. etc)
function handleCategoryFilterChange(event) {
  selectedCategory.value = event.target.value;
  fetchRoutines(searchQuery.value); // 기본 정렬로 카테고리 필터 적용
}

// 페이지 로드 시 기본 정렬된 데이터를 가져오기
onMounted(() => {
  fetchRoutines();
});

// 좋아요 버튼 클릭 시 동작
async function toggleLike(communityId) {
  const routine = routineCommunityArray.value.find(
    (r) => r.communityId === communityId
  );

  // 현재 사용자의 ID (로그인된 사용자 정보를 활용)
  const userId = localStorage.getItem('userId');   // 실제 로그인된 사용자 ID로 대체

  // 좋아요 취소 (이미 좋아요를 누른 상태)
  if (isLiked(communityId)) {
    routine.habitLikes -= 1;
    likesArray.value = likesArray.value.filter(
      (like) => like.communityId !== communityId
    );

    // 좋아요 취소 API 호출 (DELETE)
    try {
      await axios({
        method: 'delete',
        url: 'http://localhost:8080/routine-community/like',
        data: { userId, communityId }, // `DELETE`에서 `data`를 사용하려면 명시적 형식으로 사용
        headers: {
          'Content-Type': 'application/json', // JSON 형식 명시
        },
      });
      console.log('좋아요 취소 성공');
    } catch (error) {
      console.error('좋아요 취소 실패', error);
      routine.habitLikes += 1;  // 실패 시 복구
    }
  }
  // 좋아요 추가 (아직 좋아요를 누르지 않은 상태)
  else {
    routine.habitLikes += 1;
    likesArray.value.push({ communityId });

    // 좋아요 추가 API 호출 (POST)
    try {
      await axios.post('http://localhost:8080/routine-community/like', {
        userId,
        communityId,
      });
      console.log('좋아요 추가 성공');
    } catch (error) {
      console.error('좋아요 추가 실패', error);
      routine.habitLikes -= 1;  // 실패 시 복구
    }
  }
}

// 좋아요 여부 확인 함수
function isLiked(communityId) {
  return likesArray.value.some((like) => like.communityId === communityId);
}

async function addHabitToMyHabit(communityId) {
  const userId = localStorage.getItem('userId');  // 실제 로그인된 사용자 ID로 변경

  try {
    // POST 요청에서 params를 통해 userId와 habitId 전달
    await axios.post('http://localhost:8080/routine-community/challenge', null, {
      params: {
        userId: userId,
        habitId: communityId,  // communityId를 habitId로 전달
      }
    });
    console.log('습관이 MyHabit에 추가되었습니다.');
    alert('습관이 MyHabit에 추가되었습니다.');  // 성공 시 알림 표시
  } catch (error) {
    console.error('MyHabit 추가 중 오류 발생:', error);
  }
}
//////////////////////////////////////////////////////////////////////////////////////////////////////
// SHOT PREVIEW
// const posts = ref([]);

// 선택된 habit_id 저장 변수 (아무것도 클릭하지 않았을 때는 null)
const selectedHabitId = ref(null);

// // 특정 habit_id에 맞는 post 데이터를 가지고오는 함수
// async function selectHabit(habitId) {
//   console.log("Selected habitId:", habitId);  // habitId 값 로그 출력

//   if (selectedHabitId.value === habitId) {
//     selectedHabitId.value = null;
//     posts.value = [];  // habit이 해제되면 posts 초기화
//   } else {
//     selectedHabitId.value = habitId;
//     try {
//       // const response = await axios.get(`http://localhost:8080/routine-community/search-or-sort`, 
//       const response = await axios.get(`http://localhost:8080/routine-community/posts/${habitId}`);
//       console.log("resoponseDATA",response.data);
//       posts.value = response.data.map(post => ({
//         post_id: post.postId,
//         habit_id: post.habitId,
//         imageUrl: post.imageURL
//       }));

//       //   // **여기서 이미지 URL 로그 확인**
//       //   posts.value.forEach(post => {
//       //   console.log("Image URL:", post.imageUrl);  // 이미지 URL 출력
//       // });
//       console.log('Fetched posts:', posts.value);
//     } catch (error) {
//       console.error('Error fetching posts:', error);
//     }
//   }
// }
// 루틴 클릭 시 SHOT PREVIEW에 해당 루틴의 사진만 보여줌
async function selectHabit(habitId) {
  console.log("Selected habitId:", habitId);
  
  if (selectedHabitId.value === habitId) {
    selectedHabitId.value = null;
    previewPosts.value = [];  // habit이 해제되면 posts 초기화
  } else {
    selectedHabitId.value = habitId;
    try {
      const response = await axios.get(`http://localhost:8080/routine-community/posts/${habitId}`);
      previewPosts.value = response.data.map(post => ({
        post_id: post.postId,
        habit_id: post.habitId,
        imageUrl: post.imageURL  // 백엔드에서 제공하는 imageURL 필드
      })).slice(0, 8);
      console.log('Fetched posts:', previewPosts.value);
    } catch (error) {
      console.error('Error fetching posts:', error);
    }
  }
}


// 선택된 habit_id에 맞는 포스트 필터링
// const filteredPosts = computed(() => {
//   if (selectedHabitId.value === null) {
//     // 아무 것도 선택되지 않았으면 전체 보여줌
//     return posts.value; // 전체 포스트를 반환
//   } else {
//     const filtered = posts.value.filter((post) => post.habit_id === selectedHabitId.value);
//     console.log("Filtered posts:", filtered);
//     return filtered.length > 0 ? filtered : []; // 필터링 결과가 없으면 빈 배열 반환
//   }
// });
//////////////////////////////////////////////////////////////////////////////////////////////////////



// // 검색어로 루틴 데이터를 가져오는 함수
// const searchRoutines = async (keyword) => {
//   try {
//     console.log('Search API called with:', keyword); // API 호출 전에 로그
//     const response = await axios.get(`http://localhost:8080/routine-community/search`, {
//       params: {
//         keyword: keyword,
//         categoryName: selectedCategory.value !== 'all' ? selectedCategory.value : null,
//         sortType: currentSortType.value || 'recent',
//       },
//     });
    
//     console.log('Received response from API:', response.data); // API 응답을 로그로 출력
//     routineCommunityArray.value = response.data; // routineCommunityArray에 데이터 저장
//     console.log('Updated routineCommunityArray:', routineCommunityArray.value); // 업데이트된 배열 확인
//   } catch (error) {
//     console.error('검색 중 오류 발생:', error); // 오류 발생 시 로그 출력
//   }
// };
function logImageUrl(imageUrl) {
  console.log("Image URL:", imageUrl);
}

</script>

<style scoped>
@font-face {
  font-family: 'Wanted Sans';
  src: url('@/assets/fonts/WantedSans-Regular.ttf') format('truetype');
  font-weight: normal;
  font-style: normal;
}

* {

    font-family: "Wanted Sans";

}

/* 화면 줄이면 !!*/
@media (max-width: 850px) {
  .main {
    grid-template-columns: 1fr; /* 한 줄로 변경 */
  }

  /* intro는 헤더 바로 아래에 */
  .intro {
    /* grid-column: 1; 첫 번째 열에 위치 */
    display: none;
  }

  /* cards는 화면 가운데 */
  .cards {
    grid-column: 1; /* cards도 1열 */
    display: flex;
    justify-content: center;
    width: 100%; /* 가로 너비 채우기 */
  }
}

/* 전체 배치 */

.filter {
  margin: 3% 8%;
  display: grid;
  grid-template-columns: 2fr;
}

.main {
  margin: 2% 8%;

  display: grid;
  /* grid-template-columns: 20% 80%; */
  grid-template-columns: 1fr 3fr;
  /* grid-template-rows: auto 1fr; */
  grid-gap: 20px; /* 그리드 간의 간격 */
}

.categoryFilter {
  grid-column: 1;
}

.otherFilter {
  grid-column: 2;
}

.intro {
    margin: 3% 8%;
  grid-column: 1 / span 2; /* 소개는 두 열을 모두 차지 */
  display: flex;
  justify-content: flex-end;
  align-items: center;
}
.introLeft {
  flex: 1;
}
.search-routine {
  flex: 0.2;
}
/* .col {
    flex: 1 1 30%; 
    min-width: 200px; 
} */

.cards {
  /* width: 80%; */
  /* justify-self: end; 오른쪽에 정렬 */
  grid-column: 2;
  /* min-width: 200px; 비었을 때 최소 높이 */
}





/* 1등, 2등, 3등 표시 */
.cards .col {
  position: relative;
}

.cards .col:nth-child(1) .card::before {
  content: "1st";
  position: absolute;
  top: -4px; /* 삼각형의 위치를 조금 위로 조정 */
  left: -4px; /* 왼쪽으로 살짝 배치 */
  background-color: gold;
  color: white;
  width: 50px; /* 삼각형의 너비 */
  height: 50px; /* 삼각형의 높이 */
  clip-path: polygon(0 0, 100% 0, 0 100%); /* 삼각형 모양 */
  font-weight: bold;
  font-size: 14px; /* 글씨 크기 */

  display: flex;
  padding-left: 5px; /* 텍스트를 왼쪽으로 이동 */
  padding-top: 5px; /* 텍스트를 위쪽으로 이동 */
  text-align: left; /* 텍스트 정렬을 왼쪽으로 */
}

.cards .col:nth-child(2) .card::before {
  content: "2nd";
  position: absolute;
  top: -4px;
  left: -4px;
  background-color: silver;
  color: white;
  width: 50px;
  height: 50px;
  clip-path: polygon(0 0, 100% 0, 0 100%);
  font-weight: bold;
  font-size: 14px;
  padding-left: 5px; /* 텍스트를 왼쪽으로 이동 */
  padding-top: 5px; /* 텍스트를 위쪽으로 이동 */
  text-align: left;
}

.cards .col:nth-child(3) .card::before {
  content: "3rd";
  position: absolute;
  top: -4px;
  left: -4px;
  background-color: #cd7f32; /* 동메달 색상 */
  color: white;
  width: 50px;
  height: 50px;
  clip-path: polygon(0 0, 100% 0, 0 100%);
  font-weight: bold;
  font-size: 14px;
  padding-left: 5px; /* 텍스트를 왼쪽으로 이동 */
  padding-top: 5px; /* 텍스트를 위쪽으로 이동 */
  text-align: left;
}



.cards .col:nth-child(1) .card {
    box-shadow: 0 1px 3px #ffd9008a, 0 1px 2px #ffd9008a;
}
.cards .col:nth-child(2) .card {
    box-shadow: 0 1px 3px #c0c0c0ac, 0 1px 2px #c0c0c0ac;
}
.cards .col:nth-child(3) .card {
    box-shadow: 0 1px 3px #cd8032ab, 0 1px 2px #cd8032ab;
}








/* ************ 인트로 세부 ************ */
.intro p {
  /* margin-top: 10%; */
  margin-bottom: 6%;
  font-weight: 500;
}

.intro h3 {
    font-weight: 900;
}

/* ************ 카드 세부 ************ */
.card {
  border-radius: 0;
}

.card-title {
  margin-top: 5%;
  font-weight: 900;
}

.subtitle {
  display: flex;
}
.subtitle .type {
  background-color: black;
  color: white;
  font-size: 12px;
  padding: 2%;
  margin-bottom: 10%;
}
.subtitle .date {
  font-size: 12px;
  color: rgb(82, 82, 82);
  margin-left: auto;
}

/* 카드 안의 작성자, 좋아요 배치 */
.card-text {
  display: flex;
  justify-content: space-between;
  /* 양쪽 끝에 붙이고, 가운데에 균등한 공간을 배치!! 부모 영역에 space-between 걸면 자식요소들이..*/
  /* |Item 1            Item 2            Item 3| */
  align-items: center;
}

/* 작성자 아바타 & 닉네임 */
.avatar {
  width: 18px;
  height: 18px;
  border-radius: 50%;
}

.writerName {
  font-size: 12px;
  margin-left: 5px;
}

/* 좋아요 컨테이너 */
.likeContainer {
  display: flex;
  flex-direction: column; /* 세로 정렬 */
  align-items: flex-end; /* 오른쪽 정렬 */
}

.likeButton {
  border: none;
  background-color: transparent;
}

.likeImg {
  width: 22px; /* 아이콘 크기 조절 */
  margin-bottom: -3px; /* like와 likeComment 사이의 간격 */
  margin-right: 0px;
  /* border: 0px; */
}

.likeComment {
  font-size: 11px; /* 텍스트 크기 */
  text-align: right; /* 오른쪽 정렬 */
  margin-right: 13px;
}

/* 도전 컨테이너 -- 멘트 & 버튼 */
.challengeContainer {
  display: flex; /* flexbox 레이아웃 활성화 */
  flex-direction: column; /* 요소들을 수직으로 쌓기 */
  justify-content: center;
}
.challengeComment {
  font-size: 13px;
  margin-top: 20%;
  margin-bottom: 5px;
  text-align: center;
}
.challengeIcon {
  width: 18px;
  margin-right: 2px;
}

.challengeButton {
  background-color: rgb(0, 0, 0);
  border: 0;
  border-radius: 0;
}
.challengeButtonText {
  font-size: 14px;
}



/* 인증사진 미리보기 */
.shots {
    grid-column: 1;
    background-color: #d3be5544;
    margin-right: 100px;
    padding: 8px;
    width: 100%;
}

.shots h5 {
    margin-bottom: 20px;
    text-decoration: underline;
}

.shots p {
    margin-top: 0;
    margin-bottom: 15px;
    font-size: 14px;
}

/* 카드 높이를 제한하고, 이미지 크기 조정 */
.shots .card {
    height: auto; /* 카드가 부모 요소 내에서 가득 차도록 설정 */
    display: flex;
    flex-direction: column;
    border: none;
    background-color: transparent;
}

.shots .card img {
    width: 130px;
    height: 130px; /* 이미지 높이를 자동으로 맞추기 */
    object-fit: cover; /* 이미지 비율 유지 */

    border-radius: 0;
}


/* 카드를 조밀하게 배치 */
.shots .row .col {
    padding: 0; /* 여백 제거 */
    margin-bottom: 0; /* 행 간 여백 제거 */
}
.pagination {
        margin:24px;
    }
    .pagination li {
        min-width:32px;
        padding:2px 6px;
        text-align:center;
        margin:0 3px;
        border-radius: 6px;
        border:1px solid #eee;
        color:#666;
    }
    .pagination li:hover {
        background: #E4DBD6;
    }
    .page-item a {
        color:#666;
        text-decoration: none;
    }
    .pagination li.active {
        background-color : #E7AA8D;
        color:#fff;
    }
    .pagination li.active a {
        color:#fff;
    }


</style>

