<template>
<div className="sidenav">
  <p className="sidenavbigtext">Demo Test</p>

  <div className="sidemenu">
    <div className="sidenavigation">
      <div className="iconandtext">
        <img src="./assets/iconuser24.svg"/>
        <p className="sidenavigationtext">Profile</p>
      </div>
    </div>
    <div className="sidenavigationactive">
      <div className="iconandtext">
        <img src="./assets/iconvechicles.svg"/>
        <p className="sidenavigationtext">Vechicles</p>
      </div>
    </div>
    <div className="sidenavigation">
      <div className="iconandtext">
        <img src="./assets/iconsettings.svg"/>
        <p className="sidenavigationtext">Setting</p>
      </div>
    </div>
  </div>


  <div className="sidewave">
    <img src="./assets/Vector.svg" className="waveimg" />
  </div>
</div>

<div className="header">
  <p className="HeaderBigText">Vechicles</p>
  <div className="Badge">
      <p className="HeaderSmallText1" v-if="CurrentPage" :key="CurrentPage.meta.total">{{CurrentPage.meta.total}}</p>
  </div>
  <div className="AddButton">
    <img src="./assets/iconaddbutton.svg" />
  </div>
  <div className="UserBlock">
    <div className="UserPhoto">
      <img src="./assets/johnimage.png" />
    </div>
    <p className="HeaderSmallText2">John Doe</p>
  </div>
  <div className="LanguageBlock">
    <img src="./assets/Languages/uk.svg" style="padding-right: 14px;"/>
    <p className="HeaderSmallText2">En</p>
    <img src="./assets/chevron_down.svg" style="padding-left: 3px;"/>
  </div>
</div>

<div className="SearchFormDiv">
  <input type="text" className="SearchForm" placeholder="Search VIN"  @keyup.enter="SearchEnter($event.target.value);" @input="SearchInput($event.target.value);"/>
  <div className="SearchIcon" @click="SearchEnter($event.target.value);">
  <img src="./assets/iconsearch.svg" />
  </div>
</div>

<div className="CarPageSelector">
  <p className="SomeSmallText">Select vehicles per page:</p>
  <div className="CarPerPageForm">
    <p className="Option" v-if="PerPageUpdater">{{PerPageUpdater}}</p>
    <div className="ArrowButton2" @click="PerPageDropDown()">
    <img src="./assets/chevron_down.svg" className="Arrow" />
    </div>
  </div>
<Transition name="DropDownMenu">
  <div className="CarPerPageFormOptions" v-if="Selector" :key="Selector">
    <div className="CarPerPageOption" id="Option6" @click="NewPerpage(6)">
      <p className="Option">6</p>
    </div>
    <div className="CarPerPageOption" id="Option9" @click="NewPerpage(9)">
      <p className="Option">9</p>
    </div>
    <div className="CarPerPageOption" id="Option12" @click="NewPerpage(12)">
      <p className="Option">12</p>
    </div>
  </div>
</Transition>
</div>

<div className="AddVecButton">
  <div className="AddVecButtonBlock">
    <img src="./assets/iconplus.svg" />
    <p className="SomeSmallText2">ADD VECHICLE</p>
  </div>
</div>

<div className="MainGroup">
<div className="CardsBlock" v-if="CurrentPage" :key="CurrentPage.data">
  <div className="Card" v-for="(item, index) in CurrentPage.data.slice(0, PerPageUpdater)" :key="item.vehicle_name">
    <div className="TripleDotAnimation">
    <img src="./assets/more_horizontal.svg" className="tripledot" />
    </div>
    <img :src="item.preview" className="CarPhoto" />
    <p className="SomeBigText">{{ item.vehicle_name }}</p>
    <p className="SomeSmallText3">{{ item.vin }}</p>
    <div className="SomeLine"></div>
    <div className="Badge2">
      <p className="HeaderSmallText1">{{ item.uploads }}/30</p>
    </div>
    <p className="SomeSmallText4">3 days left</p>
  </div>
</div>
<div className="Stub" v-if="CurrentPage && CurrentPage.meta.total == 0">
  <p className="SomeSmallText7">Look's like there is nothing...</p>
  <p className="SomeSmallText7">Try to search something else.</p>
</div>
<div className="BottomThings" v-if="CurrentPage" :key="CurrentPage.meta">
    <p className="SomeSmallText5">Showing 9 out of {{CurrentPage.meta.total}}</p>
    <div className="Pagination">
      <div class="ArrowButton" @click="PrevPage();">
      <img src="./assets/chevron_down.svg" style="transform: rotate(90deg); pointer-events: none;" />
      </div>
      <input type="text" @keyup.enter="PageInput($event.target.value);" :placeholder="ActivePages()" class="PageCounter" />
      <p className="SomeSmallText6">of</p>
      <input type="text" :placeholder="TotalPages()" class="PageCounter" readonly>
      <div class="ArrowButton" @click="NextPage();">
      <img src="./assets/chevron_down.svg" style="transform: rotate(-90deg); pointer-events: none;" />
      </div>
    </div>
  </div>
</div>


</template>

<script>
import { defineAsyncComponent } from 'vue';

var page = 1;
var perpage = 9;
var search = undefined;



function CurrentFilters() 
{ 
  if (search == undefined) {
  var request = 'https://api.caiman-app.de/api/cars-test?per_page='+ perpage +'&page='+ page +'';
  } else {
  var request = 'https://api.caiman-app.de/api/cars-test?search='+ search +'&per_page='+ perpage +'&page='+ page +'';
  }

  return request;

}

export default {
data() {
return {
CurrentPage: null,
request: CurrentFilters(),
Selector: false,
PerPageUpdater: perpage,
SearchUpdater: '',
}
},
mounted() {
this.getCurrentPageData();
},
methods: {
getCurrentPageData() {
fetch(this.request)
.then(response => response.json())
.then(data => {
if (data.meta.current_page <= data.meta.last_page) {
  this.CurrentPage = data;
} else {
  page = data.meta.last_page;
}
this.PerPageCounter();
})
.catch(error => {
console.error('Произошла ошибка:', error);
});
},
NextPage() {
  
  if (page < this.CurrentPage.meta.last_page ) {
  page = page + 1;
  this.request = CurrentFilters(page);
  this.getCurrentPageData();
  }
},
PrevPage() {
  if (page > 1) {
  page = page - 1;
  this.request = CurrentFilters(page);
  this.getCurrentPageData();
  }
},
PageInput(x) {
if (x > 0 && x <= this.CurrentPage.meta.last_page) {
  page = x;
  this.request = CurrentFilters(page);
  this.getCurrentPageData();
}
},
TotalPages() {
  return this.CurrentPage.meta.last_page;
},
ActivePages() {
  return this.CurrentPage.meta.current_page;
},
PerPageCounter() {
  if (perpage != this.PerPageUpdater) {
    this.PerPageUpdater = perpage;
  }
},
NewPerpage(x) {
  perpage = x;
  this.request = CurrentFilters();
  this.getCurrentPageData();
  this.Selector = false;
},
PerPageDropDown(){
  this.Selector =!this.Selector;
},
SearchInput(x) {
  if (x == '') {
    search = undefined;
    this.request = CurrentFilters(page);
    this.getCurrentPageData();
  }
  this.SearchUpdater = x;
},
SearchEnter(x) {
  search = this.SearchUpdater;
  this.request = CurrentFilters(page);
  this.getCurrentPageData();
}
},
}

</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=DM+Sans:ital,opsz,wght@0,9..40,100..1000;1,9..40,100..1000&display=swap');


.sidenav {
/* Sidenav/dark */

position: fixed;
width: 256px;
left: 0px;
top: 0;
bottom: 0;

background-color: #282828;
}

.sidenavbigtext {
/* Demo Test */

position: absolute;
width: auto;
height: 24px;
left: 56px;
top: 30px;
margin: 0px;
font-family: 'DM Sans';
font-style: normal;
font-weight: 700;
font-size: 24px;
line-height: 24px;
/* identical to box height, or 100% */

color: #FFFFFF;
}


.sidewave {
/* wave */

position: absolute;
left: 0%;
right: 0%;
top: 26.54%;
bottom: 0%;
}

.waveimg {
position: absolute;
bottom: 0px;
}

.sidemenu {
/* menu */

display: flex;
flex-direction: column;
position: absolute;
left: 0%;
right: 0%;
top: 11.23%;
bottom: 71.58%;
gap: 16px;
}

.sidenavigation {
/* nav-item/default */
position: relative;
width: 256px;
height: 48px;
left: 0px;
top: 0px;
background-color: rgb(255, 255, 255, 0.0);
transition: background-color 0.3s ease-in-out;
}

.sidenavigation:hover {
  background-color: rgb(255, 255, 255, 0.1);
}

.sidenavigationactive {
/* nav-item/active */
position: relative;
width: 256px;
height: 48px;
left: 0px;
top: 0px;
border-left: solid;
box-sizing: border-box;
border-color: #D90E32;
/* Text/White_20 */
background: rgba(255, 255, 255, 0.2);

}

.iconandtext {
/* icon-text */

/* Auto layout */
display: flex;
flex-direction: row;
align-items: flex-start;
padding: 0px;
gap: 20px;

position: absolute;
width: 198px;
height: 24px;
left: calc(50% - 198px/2 + 1px);
top: calc(50% - 24px/2);
}

.sidenavigationtext {
/* Profile */

width: 154px;
height: 24px;

/* Navigation */
font-family: 'DM Sans';
font-style: normal;
font-weight: 500;
font-size: 16px;
line-height: 20px;
/* or 125% */
display: flex;
align-items: center;
margin: 0;
/* Text/White_45 */
color: rgba(255, 255, 255, 0.45);


/* Inside auto layout */
flex: none;
order: 1;
flex-grow: 0;

}

.header {
/* Header top */

position: absolute;
width: calc(100% - 256px);
height: 72px;
left: 256px;
top: 30px;
border-bottom: 1px solid;
box-sizing: border-box;
border-color: #E4E4E4;

}

.HeaderBigText {
/* Vechicles */

position: absolute;
width: 155px;
height: 38px;
left: 30px;
top: 4px;
margin: 0px;
/* H1 */
font-family: 'DM Sans';
font-style: normal;
font-weight: 700;
font-size: 32px;
line-height: 38px;
/* identical to box height, or 119% */
display: flex;
align-items: center;

/* Text/Black */
color: #293148;
}



.Badge{
/* elements */

/* Auto layout */
display: flex;
flex-direction: row;
align-items: center;
justify-content: center;

position: absolute;
width: 56px;
height: 30px;
left: 203px;
top: 8px;

/* Primary/Light Grey */
background: #F3F6F8;
border-radius: 6px;

}

.HeaderSmallText1 {
/* 256 */

width: fit-content;
height: fit-content;
margin: 0;
font-family: 'DM Sans';
font-style: normal;
font-weight: 700;
font-size: 15px;
line-height: 22px;
/* identical to box height, or 147% */
display: flex;
align-items: center;
text-align: center;

/* Text/Dark Grey */
color: rgba(41, 49, 72, 0.8);


/* Inside auto layout */
flex: none;
order: 0;
flex-grow: 0;

}

.AddButton {
/* Button/Outline-icon/Default */

box-sizing: border-box;

position: absolute;
width: 42px;
height: 42px;
right: 316px;
top: 2px;

display: flex;
align-items: center;
justify-content: center;
/* Secondary/Light Red */
border: 1px solid #E86E84;
box-sizing: border-box;
border-radius: 6px;

}

.UserBlock {
/* User */

position: absolute;
width: 131px;
height: 46px;
right: 145px;
top: 0px;
gap: 14px;
display: flex;
}

.HeaderSmallText2 {
/* John Doe */

position: relative;
width: auto;
height: auto;
margin: 0;
/* Text 3 */
font-family: 'DM Sans';
font-style: normal;
font-weight: 500;
font-size: 15px;
line-height: 22px;
/* or 147% */
display: flex;
align-items: center;

/* Text/Black */
color: #293148;

}

.UserPhoto {
/* John Doe */

position: relative;
right: 0;

/* Text 3 */
font-family: 'DM Sans';
font-style: normal;
font-weight: 500;
font-size: 15px;
line-height: 22px;
/* or 147% */
display: flex;
align-items: center;

/* Text/Black */
color: #293148;

}

.LanguageBlock {
/* Language */

position: absolute;
width: 83px;
height: 24px;
right: 30px;
top: 11px;
display: flex;
}

.SearchFormDiv {
/* Form/Search/Default */

box-sizing: border-box;
overflow: hidden;
position: absolute;
width: 354px;
height: 42px;
left: 286px;
top: 138px;
border: 1px solid #E4E4E4;
border-radius: 8px;

display: flex;
flex-direction: row;
align-items: center;
padding: 0px 16px;
gap: 16px;
}

.SearchForm {
width: 100%;
height: fit-content;
outline: none !important;
border: none;
padding: 0;
/* Body */
font-family: 'DM Sans';
font-style: normal;
font-weight: 400;
font-size: 16px;
line-height: 22px;
z-index: 6;

/* Text/Dark Grey */
color: rgba(41, 49, 72, 0.8);

}

.CarPageSelector {
/* Select */

position: absolute;
display: flex;
flex-direction: row;
justify-content: flex-start;
align-items: center;
gap: 16px;
width: 285px;
height: 42px;
left: 672px;
top: 138px;
}

.CarPerPageFormOptions {
/* Form/Input */

box-sizing: border-box;
position: absolute;
display: flex;
flex-direction: column;
height: 126px;
width: 85px;
right: 0px;
top: 45px;
z-index: 5;
/* Text/Line Gray */
border: 1px solid #E4E4E4;
border-radius: 8px;
gap: 1px;
padding: 2px;
background-color: #ffffff;
}

.CarPerPageOption {
  /* Form/Input */

box-sizing: border-box;
position: relative;
height: 42px;
width: auto;
z-index: 5;
/* Text/Line Gray */
padding: 9px 16px;
border-radius: 8px;
background-color: #FFFFFF;
}



.SomeSmallText {
/* Select vehicles per page: */

position: relative;

/* Body */
font-family: 'DM Sans';
font-style: normal;
font-weight: 400;
font-size: 16px;
line-height: 22px;
/* identical to box height, or 138% */
text-align: center;
margin: 0;
left: 0;
height: auto;
/* Text/Black */
color: #293148;

}

.CarPerPageForm {
/* Form/Input */

box-sizing: border-box;
position: relative;
height: 42px;
width: 85px;
/* Text/Line Gray */
border: 1px solid #E4E4E4;
border-radius: 8px;
padding: 9px 16px;
}

.Option {
/* 9 */

position: absolute;
width: 163px;
height: 24px;
left: 16px;
top: 9px;
margin: 0;
/* Body */
font-family: 'DM Sans';
font-style: normal;
font-weight: 400;
font-size: 16px;
line-height: 22px;
/* or 138% */
display: flex;
align-items: center;
outline: none !important;
border: none;
/* Text/Black */
color: #293148;

}

.Arrow {
position: relative;
}

.AddVecButton {
/* Button/Min-Solid/Default */

position: absolute;
width: 180px;
height: 42px;
right: 32px;
top: 138px;
/* Primary/Red */
background: #D90E32;
/* Shadow */
box-shadow: 0px 8px 24px rgba(217, 14, 50, 0.12);
border-radius: 10px;
transition: background-color 0.5s ease-in-out;
 
}

.AddVecButton:hover {
  background: #e53935;
}

.AddVecButtonBlock {
/* Frame 7 */

position: absolute;
width: 144px;
height: 24px;
left: 18px;
top: 9px;

display: flex;
flex-direction: row;
gap: 18px;
}

.SomeSmallText2 {
/* Default */

position: relative;
width: 230px;
height: 24px;
margin: 0;
/* Button 2 */
font-family: 'DM Sans';
font-style: normal;
font-weight: 700;
font-size: 12px;
line-height: 20px;
/* or 167% */
display: flex;
align-items: center;
letter-spacing: 1px;
text-transform: uppercase;

/* Text/White */
color: #FFFFFF;

}

.CardsBlock {
/* Cards */

position: relative;
height: fit-content;
width: 100%;
display: grid;
grid-template-columns: repeat(auto-fill, 354px);
gap: 30px;
justify-content: center;
}

.Card {
/* Card */

position: relative;
width: 354px;
height: 335px;
background: #F3F6F8;
border-radius: 10px;

}

.tripledot {
/* Icons/24/more_horizontal */

position: relative;


}

.CarPhoto {
/* mersedes 1 */

position: absolute;
width: 260px;
height: 135px;
left: 13.28%;
right: 13.28%;
top: 11.94%;
bottom: 47.76%;
object-fit: cover;
border-radius: 10px;
}

.SomeBigText {
/* Mercedes-Benz C-Class */

position: absolute;
left: 6.78%;
right: 11.02%;
top: 59.4%;
bottom: 34.03%;
margin: 0;
font-family: 'DM Sans';
font-style: normal;
font-weight: 700;
font-size: 20px;
line-height: 22px;
/* identical to box height, or 110% */
display: flex;
align-items: center;

/* Text/Black */
color: #293148;

}

.SomeSmallText3 {
/* WDB 1400321A333419 */

position: absolute;
left: 6.78%;
right: 47.46%;
top: 69.55%;
bottom: 24.48%;
margin:0;
font-family: 'DM Sans';
font-style: normal;
font-weight: 500;
font-size: 15px;
line-height: 20px;
/* identical to box height, or 133% */
display: flex;
align-items: center;
letter-spacing: 0.3px;

/* Text/Grey */
color: rgba(41, 49, 72, 0.6);


}

.SomeLine {
/* Line 4 */

position: absolute;
left: 6.78%;
right: 6.78%;
top: 80.9%;
bottom: 19.1%;

/* Text/Line Gray */
border: 1px solid #E4E4E4;

}

.SomeSmallText4 {
/* 3 days left */

position: absolute;
width: 74px;
height: 16px;
right: 24px;
top: 296px;
margin: 0;
font-family: 'DM Sans';
font-style: normal;
font-weight: 500;
font-size: 15px;
line-height: 16px;
/* identical to box height, or 107% */
display: flex;
align-items: center;
text-align: right;

/* Text/Grey */
color: rgba(41, 49, 72, 0.6);

}

.Badge2{
/* elements */

/* Auto layout */
display: flex;
flex-direction: row;
align-items: center;
justify-content: center;
padding: 4px 15px;
position: absolute;
left: 6.78%;
right: 66.95%;
top: 86.27%;
bottom: 4.78%;
width: 35px;
height: 22px;
/* Primary/Light Grey */
background: #EDEDED;
border-radius: 6px;

}

.SomeSmallText5 {
/* Showing 9 out of 256 */

position: absolute;
width: 158px;
height: 22px;
left: 0px;
top: 7px;
margin: 0;

/* Body */
font-family: 'DM Sans';
font-style: normal;
font-weight: 400;
font-size: 16px;
line-height: 22px;
/* identical to box height, or 138% */
display: flex;
align-items: center;

/* Text/Dark Grey */
color: rgba(41, 49, 72, 0.8);

}

.Pagination {
/* Pagination */

position: absolute;
width: 184px;
height: 32px;
right: 0px;
top: 2px;
display: flex;
flex-direction: row;
align-items: center;
justify-content: flex-start;
gap: 10px;
}

.SomeSmallText6 {
/* of */

position: relative;
margin: 0;
font-family: 'DM Sans';
font-style: normal;
font-weight: 400;
font-size: 13px;
line-height: 24px;
/* identical to box height, or 185% */
text-align: center;

/* Text/Dark Grey */
color: rgba(41, 49, 72, 0.8);

}

.PageCounter {
/* Rectangle 5 */

box-sizing: border-box;

position: relative;
margin: 0;
width: 32px;
height: 32px;

/* Text/Line Gray */
border: 1px solid #E4E4E4;
border-radius: 6px;
outline: none;
/* Text 4 */
font-family: 'DM Sans';
font-style: normal;
font-weight: 400;
font-size: 15px;
line-height: 22px;
/* identical to box height, or 147% */
text-align: center;

/* Text/Dark Grey */
color: rgba(41, 49, 72, 0.8);

}

.BottomThings {
  position: relative;
  width: 100%;
  min-width: 77.9166666666666%;
  height: 79px;
}

.MainGroup {
position: absolute;
right: 30px;
height: fit-content;
left: 286px;
top: 212px;
width: auto;
min-width: 77.9166666666666%;
display: flex;
flex-direction: column;
gap: 30px 0px;
}

.Stub {
position: relative;
height: 700px;
display: flex;
flex-direction: column;
justify-content: center;
align-items: center;
gap: 30px 0px;
}

.SomeSmallText7 {

position: relative;

/* Body */
font-family: 'DM Sans';
font-style: normal;
font-weight: 400;
font-size: 16px;
line-height: 22px;
/* identical to box height, or 138% */
text-align: center;
margin: 0;
/* Text/Black */
color: #293148;

}

.ArrowButton {
  background-color: rgb(255, 255, 255, 0.0);
  transition: background-color 0.3s ease-in-out;
  border-radius: 8px;
  height: 32px;
  width: 32px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.ArrowButton:hover {
  background-color: rgb(0, 0, 0, 0.1);
}

.ArrowButton2 {
  background-color: rgb(255, 255, 255, 0.0);
  transition: background-color 0.3s ease-in-out;
  border-radius: 8px;
  height: 24px;
  width: 24px;
  right: 9px;
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
}

.ArrowButton2:hover {
  background-color: rgb(0, 0, 0, 0.1);
}

.TripleDotAnimation {
  background-color: rgb(255, 255, 255, 0.0);
  transition: background-color 0.3s ease-in-out;
  border-radius: 8px;
  position: absolute;
  left: 87.57%;
  right: 5.65%;
  top: 5.97%;
  bottom: 86.87%;
  height: 26px;
  width: 26px;
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
}

.TripleDotAnimation:hover {
  background-color: rgb(0, 0, 0, 0.1);
}

.SearchIcon {
  width: 30px;
  height: 26px;
  background-color: rgb(255, 255, 255, 0.0);
  transition: background-color 0.3s ease-in-out;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  border-radius: 8px;
  padding: 2px;
}

.SearchIcon:hover {
  background-color: rgb(0, 0, 0, 0.1);
}

.DropDownMenu-enter-active,
.DropDownMenu-leave-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}

.DropDownMenu-enter-from,
.DropDownMenu-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

</style>
