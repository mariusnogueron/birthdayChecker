<script setup>
import { ref, onMounted, nextTick } from "vue";
import { supabase } from "./lib/supabaseClient";
import { Logger } from "sass";

const birthdays = ref([]);

const day = ref("");
const month = ref("");
const year = ref("");
const todayDate = ref("");

const todayBirthday = ref("");
const birthNames = ref("");

function zeroDay(time) {
  return time < 10 ? `0${time}` : time;
}

const now = new Date();
day.value = zeroDay(now.getDate());
month.value = zeroDay(now.getMonth() + 1);
year.value = zeroDay(now.getFullYear());

todayDate.value = `${day.value}/${month.value}`;

async function getBirthday() {
  const { data } = await supabase.from("birthdays").select();
  const listBirthday = (birthdays.value = data);

  birthNames.value = [];
  todayBirthday.value = [];

  listBirthday.forEach((birth) => {
    let dateBirthday = new Date(birth.date);
    let dayBirthday = zeroDay(dateBirthday.getDate());
    let monthBirthday = zeroDay(dateBirthday.getMonth() + 1);
    let yearBirthday = dateBirthday.getFullYear();

    const formatedBirthdayDate = `${dayBirthday}/${monthBirthday}`;
    const fullName = ` ${birth.firstname}  ${birth.lastname}`;

    if (todayDate.value === formatedBirthdayDate) {
      todayBirthday.value.push(formatedBirthdayDate);
      birthNames.value.push(fullName);
    }
  });
}

onMounted(() => {
  getBirthday();
});
</script>

<template>
  <div class="flex gap-5 flex-col">
    <div
      class="flex justify-between flex-row bg-lime-200 p-8 rounded-xl text-4xl"
    >
      <h1>Birthdays</h1>
      <span>🎂🎂</span>
    </div>
    <div class="p-8 flex items-center justify-between">
      <h2 class="rounded-xl text-2xl p-3 bg-lime-200">Today :</h2>
      <div class="rounded-xl bg-lime-200 p-3 text-2xl">
        {{ todayBirthday }}
        {{ birthNames }}
      </div>
    </div>
  </div>
</template>
