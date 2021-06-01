<template>
  <div>
    <p>checkbox:</p>
    <label class="container"
      >opção 1
      <input type="checkbox" checked="checked" />
      <span class="checkmark"></span>
    </label>
    <label class="container"
      >opção 2
      <input type="checkbox" checked="checked" />
      <span class="checkmark"></span>
    </label>
  </div>
</template>

<style scoped>
p {
  padding-bottom: 2px;
}
.container {
  display: block;
  position: relative;
  padding-left: 22px;
  margin-bottom: 1px;
  cursor: pointer;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

.container input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

.checkmark {
  position: absolute;
  top: 2px;
  left: 4px;
  height: 12px;
  width: 12px;
  border-radius: 3px;
  background-color: white;
  border: 1px solid #d6d6d6;
}

.container:hover input ~ .checkmark {
  background-color: white;
}

.container input:checked ~ .checkmark {
  background-color: white;
}

.checkmark:after {
  content: "";
  position: absolute;
  display: none;
}

.container input:checked ~ .checkmark:after {
  display: block;
}

.container .checkmark:after {
  left: 5px;
  top: -4px;
  width: 4px;
  height: 12px;
  border: solid black;
  border-width: 0 3px 3px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
}
</style>