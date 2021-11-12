<template>
  <div>
    <input type="text" v-model="value" v-mask="'### ### ####'" />
  </div>
</template>

<script>
function getNewCursorPosition(currPosition, element) {
  if (currPosition === 1) {
    return currPosition;
  }

  const newValue = element.value;
  const oldValue = element.dataset.oldValue || "";
  const newValueSpacesCount = newValue.split(" ").length - 1;
  const oldValueSpacesCount = oldValue.split(" ").length - 1;
  let spacesDiff = newValueSpacesCount - oldValueSpacesCount;

  if (spacesDiff <= 0 || currPosition < newValue.length - 1) {
    spacesDiff = 0;
  }

  if (newValue[currPosition - 1] === " " && newValue.length > oldValue.length) {
    spacesDiff = 1;
  }

  return currPosition + spacesDiff;
}

export default {
  name: "HelloWorld",
  data() {
    return {
      value: "",
      oldValue: "",
      mask: "### ### ####",
    };
  },
  directives: {
    mask: {
      bind(element, binding) {
        const mask = binding.value;
        element.oninput = function () {
          const value = element.value;
          if (value) {
            const cursorPosition = element.selectionStart;
            const unFormattedValue = value.replaceAll(" ", "");
            let newValue = "";
            let offset = 0;

            for (let n = 0; n < mask.length; n++) {
              if (mask.charAt(n) === "#") {
                newValue += unFormattedValue.charAt(n - offset);
              } else {
                newValue += mask.charAt(n);
                offset++;
              }
            }

            element.value = newValue.trim();

            const newCursorPosition = getNewCursorPosition(
              cursorPosition,
              element
            );
            setTimeout(() => {
              element.setSelectionRange(newCursorPosition, newCursorPosition);
            }, 0);

            element.dataset.oldValue = element.value;
          }
        };
      },
    },
  },
};
</script>
