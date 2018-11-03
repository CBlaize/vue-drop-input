<template>
  <div class="autocomplete">
    <input ref="input" :id="id" type="text" :name="name" :placeholder="placeholder">
  </div>
</template>

<script>
export default {
  name: 'DropInput',
  data: () => {
    return {
      inp: null,
      currentFocus: null,
      selectedOption: null,
    }
  },
  props: {
    id: String,
    name: String,
    placeholder: String,
    options: Array
  },
  methods: {
    addActive(x) {
      /*a function to classify an item as "active":*/
      if (!x) return false;
      /*start by removing the "active" class on all items:*/
      this.removeActive(x);
      if (this.currentFocus >= x.length) this.currentFocus = 0;
      if (this.currentFocus < 0) this.currentFocus = (x.length - 1);
      /*add class "autocomplete-active":*/
      x[this.currentFocus].classList.add("autocomplete-active");
    },
    removeActive(x) {
      /*a function to remove the "active" class from all autocomplete items:*/
      for (var i = 0; i < x.length; i++) {
        x[i].classList.remove("autocomplete-active");
      }
    },
    closeAllLists(elmnt) {
      /*close all autocomplete lists in the document,
      except the one passed as an argument:*/
      var x = document.getElementsByClassName("autocomplete-items");
      for (var i = 0; i < x.length; i++) {
        if (elmnt != x[i] && elmnt != this.inp) {
          x[i].parentNode.removeChild(x[i]);
        }
      }
    }
  },
  mounted() {
    this.inp = this.$refs.input;
    /*the autocomplete function takes two arguments,
    the text field element and an array of possible autocompleted values:*/
    var vm = this;
    /*execute a function when someone writes in the text field:*/
    this.inp.addEventListener("input", function(e) {
      var a, b, i, val = this.value;
      /*close any already open lists of autocompleted values*/
      vm.closeAllLists();
      if (!val) { return false;}
      vm.currentFocus = -1;
      /*create a DIV element that will contain the items (values):*/
      a = document.createElement("DIV");
      a.setAttribute("id", this.id + "autocomplete-list");
      a.setAttribute("class", "autocomplete-items");
      /*append the DIV element as a child of the autocomplete container:*/
      this.parentNode.appendChild(a);
      /*for each item in the array...*/
      for (i = 0; i < vm.options.length; i++) {
        /*check if the item starts with the same letters as the text field value:*/
        if (vm.options[i].text.substr(0, val.length).toUpperCase() == val.toUpperCase()) {
          /*create a DIV element for each matching element:*/
          b = document.createElement("DIV");
          /*make the matching letters bold:*/
          b.innerHTML = "<strong>" + vm.options[i].text.substr(0, val.length) + "</strong>";
          b.innerHTML += vm.options[i].text.substr(val.length);
          /*insert a input field that will hold the current array item's value:*/
          b.innerHTML += "<input type='hidden' value='" + vm.options[i].value + "'>";
          /*execute a function when someone clicks on the item value (DIV element):*/
          b.addEventListener("click", function(e) {
            /*insert the value for the autocomplete text field:*/
            vm.selectedOption = vm.options.find(a => a.value === this.getElementsByTagName("input")[0].value);
            vm.inp.value = vm.selectedOption.text;
            vm.$emit('input', vm.selectedOption.value);
            /*close the list of autocompleted values,
            (or any other open lists of autocompleted values:*/
            vm.closeAllLists();
          });
          a.appendChild(b);
        }
      }
    });
    /*execute a function presses a key on the keyboard:*/
    this.inp.addEventListener("keydown", function(e) {
      var x = document.getElementById(this.id + "autocomplete-list");
      if (x) x = x.getElementsByTagName("div");
      if (e.keyCode == 40) {
        /*If the arrow DOWN key is pressed,
        increase the currentFocus variable:*/
        vm.currentFocus++;
        /*and and make the current item more visible:*/
        vm.addActive(x);
      } else if (e.keyCode == 38) { //up
        /*If the arrow UP key is pressed,
        decrease the currentFocus variable:*/
        vm.currentFocus--;
        /*and and make the current item more visible:*/
        vm.addActive(x);
      } else if (e.keyCode == 13) {
        /*If the ENTER key is pressed, prevent the form from being submitted,*/
        e.preventDefault();
        if (vm.currentFocus > -1) {
          /*and simulate a click on the "active" item:*/
          if (x) x[vm.currentFocus].click();
        }
      }
    });
    document.addEventListener("click", function (e) {
      vm.closeAllLists(e.target);
    });
  },
}
</script>

<style>
  * { box-sizing: border-box; }
  body {
    font: 16px Arial;
  }
  .autocomplete {
    /*the container must be positioned relative:*/
    position: relative;
    display: inline-block;
  }
  input {
    border: 1px solid transparent;
    background-color: #f1f1f1;
    padding: 10px;
    font-size: 16px;
  }
  input[type=text] {
    background-color: #f1f1f1;
    width: 100%;
  }
  input[type=submit] {
    background-color: DodgerBlue;
    color: #fff;
  }
  .autocomplete-items {
    position: absolute;
    border: 1px solid #d4d4d4;
    border-bottom: none;
    border-top: none;
    z-index: 99;
    /*position the autocomplete items to be the same width as the container:*/
    top: 100%;
    left: 0;
    right: 0;
  }
  .autocomplete-items div {
    padding: 10px;
    cursor: pointer;
    background-color: #fff;
    border-bottom: 1px solid #d4d4d4;
  }
  .autocomplete-items div:hover {
    /*when hovering an item:*/
    background-color: #e9e9e9;
  }
  .autocomplete-active {
    /*when navigating through the items using the arrow keys:*/
    background-color: DodgerBlue !important;
    color: #ffffff;
  }
</style>
