<template>
  <div class="post">
    <div class="user-info">
      <img :src="userIcon || defaultUserIcon" alt="User Icon" class="user-icon" />
      <span class="username">{{ userName }}</span>
    </div>
    <p class="habit-title">{{ habitData.habitTitle }}</p>

    <!-- 통합된 participants와 checkedHabit 정보 -->
    <div class="habit-info">
      <p class="participants">
        현재 이 루틴에 <strong>{{ participants }} 명</strong>이 참여 중이고,<br />
        지금까지 인증샷은 총 <strong>{{ checkedHabit }}개</strong>가 올라왔어요!
      </p>
    </div>

    <div class="post-image-container">
      <img :src="post.imageURL" alt="Post Image" class="post-image" />
    </div>
    
    <div class="post-details">
      <p class="post-content">{{ post.content }}</p>
      <p class="post-hashtag">{{ post.hashtag }}</p>
      <p class="post-date">
        {{ new Date(post.createdAt).toLocaleDateString() }}
      </p>

      <!-- 댓글 섹션 -->
      <comment-section
        v-if="post.showComments"
        :post-id="post.postId"
        @comment-change="handleCommentChange"
      ></comment-section>

      <!-- 좋아요 및 댓글 버튼 -->
      <div class="interaction-buttons">
        <div class="like-button" @click="toggleLike">
          <img :src="likeIcon" alt="Like" class="icon" />
          {{ post.postLikes }}
        </div>
        <div class="comment-button" @click="toggleComments(post)">
          <img :src="commentIcon" alt="Comment" class="icon" />
          {{ commentCounts[post.postId] || 0 }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { defineProps, defineEmits, ref, onMounted, computed } from 'vue';
import axios from 'axios';
import CommentSection from '@/components/PostCommunity/CommentSection.vue';
import defaultUserIcon from '@/assets/icons8-user-64.png';
// import userIcon from '@/assets/icons8-user-64.png';
import likeEmptyIcon from '@/assets/icons/like.png';
import likeFullIcon from '@/assets/icons/fulllike2.png';
import commentIconSrc from '@/assets/icons/comment.png';

const props = defineProps({
  post: {
    type: Object,
    required: true,
  },
});

const emit = defineEmits(['toggleLike', 'toggleComments']);

// 좋아요 및 댓글 관련 데이터
const shotCount = ref(0);
const habitData = ref({});
const participants = ref(0);
const checkedHabit = ref(0);

const existingPosts = ref([]);
const commentCounts = ref({});

// 좋아요 아이콘 설정
const likeIcon = computed(() => {
  return props.post.isLiked ? likeFullIcon : likeEmptyIcon;
});

const commentIcon = ref(commentIconSrc);

// 좋아요 버튼 클릭 시 이벤트 발생
const toggleLike = () => {
  emit('toggleLike', props.post);
};

// 댓글 버튼 클릭 시 이벤트 발생
const toggleComments = () => {
  emit('toggleComments', props.post);
};

// 데이터 가져오는 함수
const fetchData = async () => {
  try {
    const shotResponse = await axios.get(
      `http://localhost:8080/post-community/certification-count?userId=${props.post.userId}`
    );
    shotCount.value = shotResponse.data;

    const habitResponse = await axios.get(
      `http://localhost:8080/habits/find?habitId=${props.post.habitId}`
    );
    habitData.value = habitResponse.data;

    const habitCommunityResponse = await axios.get(
      `http://localhost:8080/routine-community/${props.post.habitId}`
    );
    participants.value = habitCommunityResponse.data.participants;

    const checkedHabitResponse = await axios.get(
      `http://localhost:8080/habits/checked/count?&habitId=${props.post.habitId}`
    );
    checkedHabit.value = checkedHabitResponse.data;
  } catch (error) {
    console.error('인증 횟수를 가져오는 중 오류가 발생했습니다:', error);
  }
};

// 댓글 개수 가져오는 함수
const fetchCommentCounts = () => {
  axios
    .get('http://localhost:8080/post-community/posts/comments')
    .then((response) => {
      commentCounts.value = response.data;
      const postIds = Object.keys(commentCounts.value);
      existingPosts.value = postIds.map((id) => ({ id: Number(id) }));
    })
    .catch((error) => {
      console.error('Error fetching comment counts:', error);
    });
};

// 댓글 업데이트 핸들러
const handleCommentChange = (postId) => {
  axios
    .get(`http://localhost:8080/post-community/posts/${postId}/comments/count`)
    .then((response) => {
      commentCounts.value[postId] = response.data;
    })
    .catch((error) => {
      console.error('Error fetching comment count:', error);
    });
};

 // 사용자 아이콘과 이름을 저장할 변수 선언
const userIcon = ref('');
const userName = ref('');

const fetchUserInfo = async () => {
  try {
    // postId를 통해 백엔드에서 사용자 정보를 가져오는 API 호출
    const response = await axios.get(`http://localhost:8080/post-community/getUserInfoByPostId?postId=${props.post.postId}`);
    
    // 가져온 사용자 정보에서 avatar와 name을 각각 userIcon과 userName에 매핑
    userIcon.value = response.data.avatar;
    userName.value = response.data.name;
    
    console.log("User info:", response.data);
  } catch (error) {
    console.error('사용자 정보를 가져오는 중 오류가 발생했습니다:', error);
  }
};

// 컴포넌트가 마운트될 때 호출
onMounted(() => {
  fetchData();
  // getAllPost();
  fetchUserInfo();
  fetchCommentCounts();
});
</script>

<style scoped>
.post {
  width: 100%;
  max-width: 700px;
  margin: 10px auto;
  padding: 20px;
  background-color: #fff;
  border: 0.5px solid #e6e6e6;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.user-info {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
  justify-content: flex-start; 
}

.user-icon {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  margin-right: 10px;
}

.username {
  font-weight: bold;
  color: #333;
  font-size: 14px;
}

.habit-title {
  background-color: #fdf0ca;
  border-radius: 8px;
  padding: 10px 20px;
  font-size: 14px;
  font-weight: bold;
  text-align: left; /* 왼쪽 정렬 */
  margin-bottom: 15px;
  /* display: inline-block; */
  position: relative;
  float: left;
}

.habit-title::before {
  content: '';
  position: absolute;
  top: -10px;
  left: 20px;
  border-width: 0 10px 10px;
  border-style: solid;
  border-color: transparent transparent #fdf0ca transparent;
}

.habit-info {
  background-color: #f5f5f5;
  border-radius: 8px;
  padding: 10px;
  margin-top: -5px;
  text-align: left;
  width: max-content;
  max-width: 100%;
  clear: both;
}

.participants {
  margin: 0;
  font-size: 14px;
}

.post-image-container {
  display: flex;
  justify-content: center;
  align-items: center; /* 세로 중앙 정렬 */
  width: 100%; /* 부모 요소의 전체 너비 */
}

.post-image {
  width: 400px;
  height: 400px;
  border-radius: 0;
  border: 1px solid rgb(133, 133, 133);
  margin-top: 15px;
  margin-bottom: 10px;
  object-fit: cover; /* 이미지 비율을 유지하면서 잘리게 설정 */
}

.post-details {
  margin-top: 10px;
  /* font-size: 13px; */
  text-align: left;
}

.post-hashtag {
  color: #3e4b93;
  font-size: 13px;
  
}

.post-date {
  color: rgb(82, 82, 82);
  font-size: 13px;
}

.post-content,
.post-hashtag, 
.post-date {
  margin-bottom: 8px;
}

.interaction-buttons {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 30px;
}

.like-button,
.comment-button {
  display: flex;
  align-items: center;
  cursor: pointer;
  color: #888;
  font-size: 14px;
}

.icon {
  width: 20px;
  height: 20px;
  margin-right: 5px;
}
</style>
