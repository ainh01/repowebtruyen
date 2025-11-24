<template>
  <div class="card card-carousel overflow-hidden h-100 p-0">
    <div
      id="carouselExampleCaptions"
      class="carousel slide h-100"
      data-bs-ride="carousel"
    >
      <div class="carousel-inner border-radius-lg h-100">
        <div
          class="carousel-item h-100 active"
          :style="{
            backgroundImage:
              'url(' + require('@/assets/img/slide_1.webp') + ')',
            backgroundSize: 'cover',
          }"
        >
          <div
            class="carousel-caption d-none d-md-block bottom-0 text-start start-0 ms-5"
          >
            <div
              class="icon icon-shape icon-sm bg-white text-center border-radius-md mb-3"
            >
              <i class="ni ni-camera-compact text-dark opacity-10"></i>
            </div>
            <h5 class="text-white mb-1">Get started with Together</h5>
            <p>
              Đọc truyện rất thú vị, nhưng mà đọc truyện mà không hiểu thì cũng cũng.
            </p>
          </div>
        </div>
        <div
          class="carousel-item h-100"
          :style="{
            backgroundImage:
              'url(' + require('@/assets/img/slide_2.png') + ')',
            backgroundSize: 'cover',
          }"
        >
          <div
            class="carousel-caption d-none d-md-block bottom-0 text-start start-0 ms-5"
          >
            <div
              class="icon icon-shape icon-sm bg-white text-center border-radius-md mb-3"
            >
              <i class="ni ni-bulb-61 text-dark opacity-10"></i>
            </div>
            <h5 class="text-white mb-1">Kịch tích gây cấn, hấp dẫn chỉ có tại Together</h5>
            <p>
              Kĩ năng đọc truyện chỉ cần nhìn hình, còn chữ thì không cần đọc.
            </p>
          </div>
        </div>
        <div
          class="carousel-item h-100"
          :style="{
            backgroundImage:
              'url(' + require('@/assets/img/slide_3.webp') + ')',
            backgroundSize: 'cover',
          }"
        >
          <div
            class="carousel-caption d-none d-md-block bottom-0 text-start start-0 ms-5"
          >
            <div
              class="icon icon-shape icon-sm bg-white text-center border-radius-md mb-3"
            >
              <i class="ni ni-trophy text-dark opacity-10"></i>
            </div>
            <h5 class="text-white mb-1">Chia sẽ khả năng đọc truyện cho mọi người nghe!</h5>
            <p>
              Tôi biết bạn là một chú tể Wibu, vì thế hãy cùng nhau chia sẽ niềm vui.
            </p>
          </div>
        </div>
      </div>
      <button
        class="carousel-control-prev w-5 me-3"
        type="button"
        data-bs-target="#carouselExampleCaptions"
        data-bs-slide="prev"
      >
        <span class="carousel-control-prev-icon" aria-hidden="true"></span>
        <span class="visually-hidden">Previous</span>
      </button>
      <button
        class="carousel-control-next w-5 me-3"
        type="button"
        data-bs-target="#carouselExampleCaptions"
        data-bs-slide="next"
      >
        <span class="carousel-control-next-icon" aria-hidden="true"></span>
        <span class="visually-hidden">Next</span>
      </button>
    </div>
  </div>
</template>
