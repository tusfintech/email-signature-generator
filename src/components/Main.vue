<template>
  <div class="wrapper">
    <div
      ref="signature"
      class="signature"
      style="font-family: Arial, sans-serif; text-align: left; margin: 0px; max-width: 620px"
    >
      <template v-if="state.signatureType === 'basic'">
        <BasicLayout :state="state" />
      </template>
      <template v-else>
        <SalesLayout :state="state" />
      </template>

    </div>
  </div>

  <hr />

  <div class="container is-fluid mb-4">
    <div class="columns">
      <div class="column is-narrow">
        <div class="form-contain is-align-self-flex-start is-flex-grow-0 is-flex-shrink-0">
          <div class="field">
            <label class="label">Мэдээлэл оруулах хэсэг</label>
            <div class="control">
              <div class="buttons has-addons">

              </div>
            </div>
          </div>

          <form class="form">
            <div v-if="state.signatureType === 'sales'" class="field">
              <label class="label">Staff member</label>
              <div class="control">
                <div class="select">
                  <select v-model="selectedOption" @change="handleSelection">
                    <option v-for="option in state.staffMembers" :key="option.name" :value="option.name">
                      {{ option.name }}
                    </option>
                  </select>
                </div>
              </div>
            </div>

            <div v-else class="field">
              <label class="label">Овог нэр</label>
              <div class="control">
                <input
                  v-model="state.name"
                  class="input"
                  type="text"
                  placeholder="Жаргалсайхан Баяр"
                />
              </div>
            </div>

            <div class="field">
              <label class="label">Албан тушаал</label>
              <div class="control">
                <input
                  v-model="state.jobTitle"
                  class="input"
                  type="text"
                  placeholder="Бизнес менежер"
                />
              </div>
            </div>

            

            <div class="field">
              <label class="label">И-мэйл хаяг {{ state.signatureType === 'basic' ? '' : '' }}</label>
              <p class="control is-flex-grow-1">
                <input
                  v-model="state.email"
                  class="input"
                  type="email"
                  placeholder="hello@gmail.com"
                  @keyup="state.email = $event.target.value"
                />
              </p>
            </div>

            <div class="field">
              <label class="label">Утасны дугаар {{ state.signatureType === 'basic' ? '' : '' }}</label>
              <div class="control">
                <input
                  v-model="state.phone"
                  class="input"
                  type="tel"
                  placeholder="9188-0000"
                />
              </div>
            </div>

            <div class="field" v-if="state.signatureType === 'sales'">
              <label class="label">Website link (optional)</label>
              <div class="control">
                <input
                  v-model="state.infoLink"
                  class="input"
                  type="text"
                  placeholder="To share an article, blog post or any other link"
                />
                <small class="is-size-7 has-text-grey-dark">Be sure to include the full URL, including the https://</small>
              </div>
            </div>

          
          </form>
        </div>
      </div>

      <div class="column">
        <div style="max-width: 712px; margin: 0 auto">
          <h2 class="title has-text-weight-bold is-spaced is-2 mb-5">
            Тус Финтек ББСБ ХХК-ийн и-мэйл гарын үсэг үүсгэгч
          </h2>
          <div style="max-width: 600px">
            <p class="mb-2">
              Та хажуу талд байгаа хэсгүүдийг бөглөөд гарын үсгээ хуулж аваад ашиглах боломжтой.
            </p>
            <p class="mb-5">
             Ихэнх и-мэйл программууд нь хуулж аваад, оруулахад хэвийн ажилладаг. Тэгэхдээ танд асуудал гарч байвал доорх заавруудыг уншина уу.
            </p>
          </div>
          <div class="buttons mb-5">
            <button
              class="button is-primary has-text-weight-semibold"
              @click="copyContent"
            >
              <span class="button-icon">
                <svg
                  height="21"
                  viewBox="0 0 21 21"
                  width="21"
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <g
                    fill="none"
                    fill-rule="evenodd"
                    stroke="currentColor"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    transform="translate(3 3)"
                  >
                    <path
                      d="m11.5 9.5v-7c0-1.1045695-.8954305-2-2-2h-7c-1.1045695 0-2 .8954305-2 2v7c0 1.1045695.8954305 2 2 2h7c1.1045695 0 2-.8954305 2-2z"
                    />
                    <path
                      d="m3.5 11.5v1c0 1.1045695.8954305 2 2 2h7c1.1045695 0 2-.8954305 2-2v-7c0-1.1045695-.8954305-2-2-2h-1"
                    />
                  </g>
                </svg>
              </span>
              Гарын үсэг хуулж авах
            </button>

          </div>

          <h4 class="title is-4">И-мэйл программуудын заавар</h4>

          <InstructionTabs />
        </div>
      </div>
    </div>
  </div>
  <div
    v-if="showNotification"
    class="notification is-success"
    style="
      position: absolute;
      top: 8px;
      left: 0;
      right: 0;
      margin: auto;
      width: 280px;
      z-index: 100;
    "
  >
    Амжилттай хуулагдлаа!
  </div>
</template>

<script setup>
import BasicLayout from "./BasicLayout.vue";
import SalesLayout from "./SalesLayout.vue";
import InstructionTabs from "./InstructionTabs.vue";
import { reactive, computed, ref, watch, nextTick } from "vue";

const state = reactive({
  name: "",
  jobTitle: "",
  location: "",
  email: "",
  phone: "",
  note: "",
  infoLink: "",
  signatureType: "basic",
  signatureHtml: "",
  profileUrl: "",
  staffMembers: [
    {
      name: "",
      profileUrl: ""
    },
    {
      name: "John Smith",
      profileUrl: "https://placehold.co/200?text=John"
    },
    {
      name: "Eric Andre",
      profileUrl: "https://placehold.co/200?text=Eric"
    },
    {
      name: "Steve Brule",
      profileUrl: "https://placehold.co/200?text=Steve"
    },
    {
      name: "David Liebe Hart",
      profileUrl: "https://placehold.co/200?text=David"
    },
    {
      name: "Doug Prishpreed",
      profileUrl: "https://placehold.co/200?text=Doug"
    },
    {
      name: "Pierre Sampson",
      profileUrl: "https://placehold.co/200?text=Pierre"
    },
  ],
});

const signature = ref();
const showNotification = ref(false);
const selectedOption = ref(null);

const downloadUrl = computed(() => {
  const blob = new Blob([state.signatureHtml], { type: "text/html" });
  return URL.createObjectURL(blob);
});

/**
 * Handles the selection of a staff member in the sales layout.
 * Updates the state to reflect the selected staff member's information.
 */
const handleSelection = (e) => {
  const person = state.staffMembers.find((person) => person.name === e.target.value);
  state.name = person.name;
  state.profileUrl = person.profileUrl;
};

/**
 * Downloads the generated signature as an HTML file.
 * Grabs the signature HTML to create the file content.
 * Use downloadUrl to create a link then click it.
 */
const downloadFile = () => {
  nextTick(() => {
    state.signatureHtml = signature.value.innerHTML;
    const link = document.createElement("a");
    link.href = downloadUrl.value;
    link.download = "signature.html";
    link.click();
    link.remove();
  });
};

/**
 * Copies the generated signature to the clipboard.
 * Updates the state.signatureHtml before copying.
 * Uses the Clipboard API to copy both HTML and plain text versions.
 * Displays a notification to indicate successful copying.
 */
const copyContent = (e) => {
  nextTick(() => {
    e.preventDefault();
    showNotification.value = true;

    // Manually update state.signatureHtml before copying to clipboard
    state.signatureHtml = signature.value.innerHTML;

    const str = state.signatureHtml;
    function listener(e) {
      e.clipboardData.setData("text/html", str);
      e.clipboardData.setData("text/plain", str);
      e.preventDefault();
    }
    document.addEventListener("copy", listener);
    document.execCommand("copy");

    document.removeEventListener("copy", listener);
    setTimeout(() => {
      showNotification.value = false;
    }, 4000);
  });
};

// Watch for changes to the signatureHtml and update the state
watch(
  () => state,
  () => {
    state.signatureHtml = signature.value.innerHTML;
  },
  { deep: true }
);

</script>

<style scoped>
@import "https://cdn.jsdelivr.net/npm/bulma@0.9.4/css/bulma.min.css";

html,
body {
  height: 100%;
}

.wrapper {
  display: flex;
  margin-top: 20px;
}

.form-contain {
  width: 100%;
  max-width: 360px;
  min-width: 300px;
}

.logo {
  width: 100%;
  max-width: 100%;
  padding: 0 5rem;
}

.st0 {
  fill: #1ca2ca;
}

.button-icon {
  display: inline-flex;
  margin-right: 0.4rem;
  margin-left: -0.4rem;
}
</style>
