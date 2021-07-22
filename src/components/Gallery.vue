<template>
    <div class="gallery">
        <div class="container">
            <button class="arrow-btn" @click="decrementSelectedIndex" v-bind:disabled="selectedIndex == 0">
                <img src="../assets/Arrow-right.png" alt="to the previous" class="arrow-img">
            </button>
            <div class="current-img-wrapper">
                <img :src='this.imgs[selectedIndex]' alt="content" class="current-img">
            </div>
            <button class="arrow-btn" @click="incrementSelectedIndex" v-bind:disabled="selectedIndex == imgs.length - 1">
                <img src="../assets/Arrow-left.png" alt="to the next" class="arrow-img">
            </button>
        </div>
        <div class="imgs-box">
            <div v-for="(item,index) of selectedGroupImgs" :key="item" class="gallery-img-wrapper" @click="changeSelectedImg(item)"
            @mouseover="changeUnderMouse(item, index)" @mouseleave="underMouse = -1">
                <img v-show="item" :src="item" alt="content" class="gallery-img">
                <div v-show="(index != selectedIndex - (selectedGroup-1)*imgsInGroup) && item" class="gallery-img-shadow">
                </div>
                <div v-show="index == underMouse" class="delete-ico-wrapper">
                <img src="../assets/Delete.png" alt="delete" class="delete-ico" @click.stop="deleteImg(item)">
                </div>
            </div>
        </div>
        <div class="container groups">
            <img src="../assets/Arrow-right.png" alt="" class="arrow-img" @click="changeSelectedGroup(-1)">
            <p class="group-counter">
                {{selectedGroup}} / {{groupCount}}
            </p>
            <img src="../assets/Arrow-left.png" alt="" class="arrow-img" @click="changeSelectedGroup(1)">
        </div>
        <div class="container">
            <input @change="uploadImg($event)" type="file" accept=".jpg, .jpeg, .png" name="upload-img" id="upload-img" class="upload-btn">
            <input type="button" value="Upload new image" class="upload-btn" onclick="document.getElementById('upload-img').click();" />
            <input type="button" value="Upload from Flickr" class="upload-btn" @click="uploadImgFlickr">
        </div>
    </div>    
</template>

<script>
import img from "../assets/Tie_dye.jpg";
import img1 from "../assets/Vaporwave_wallpaper.jpg";
import img2 from "../assets/Three.jpg";
import img3 from "../assets/Moon.jpg";
import img4 from "../assets/Mansion.jpg";
import img5 from "../assets/Lotus.jpg";
import img6 from "../assets/huntington bancshares.jpg";
import img7 from "../assets/Hubble_Extreme_Deep_Field.jpg";
import img8 from "../assets/Forest.jpg";
import img9 from "../assets/Bubbles.jpg";

export default {
    name: 'Gallery',
    data() {
        return {
            imgs: [img, img1, img2, img3, img4, img5, img6, img7, img8, img9],
            selectedIndex: 0,
            selectedGroup: 1,
            imgsInGroup: 9,
            underMouse: -1
        }
    },
    computed: {
        groupCount() {
            if (this.imgs.length != 0) {
                return Math.ceil(this.imgs.length / this.imgsInGroup);
            }
            else return 1;
        },
        selectedGroupImgs () {
            let result = [];
            for (let i = (this.selectedGroup-1)*this.imgsInGroup; i<this.selectedGroup*this.imgsInGroup; i++) {
                result.push(this.imgs[i]);
            }
            return result;
        }
    },
    methods: {
        uploadImg(event) {
            this.imgs.push(URL.createObjectURL(event.target.files[0]));
            this.selectedIndex = this.imgs.length - 1;
            this.selectedGroup = this.groupCount;
        },
        uploadImgFlickr() {
            let a = this.uploadRequest();
            a.then(data => {
                if (data && this.imgs.indexOf(data) < 0) {
                    this.imgs.push(data);
                    this.selectedIndex = this.imgs.length - 1;
                    this.selectedGroup = this.groupCount;
                }
            });                      
        },
        async uploadRequest() {
            let request = 'https://api.flickr.com/services/rest/?method=flickr.photos.search&format=json&nojsoncallback=1&api_key=9c0b191a1d8415714a70a2a3db4abdeb&extras=url_m&text=nature'; 
            return fetch(request)
                .then(
                    function(response) {
                    if (response.status !== 200) {
                        console.log('Something went wrong. Status Code: ' + response.status);
                        return;
                    }
                    return response.json();})
                .then(function(data) {
                        return data.photos.photo[Math.ceil(Math.random() * 100)-1].url_m;
                    })
                .catch(function(err) {
                    console.log('Fetch Error:', err);
                });
        },
        changeSelectedImg(item) {
            if (item !== undefined) {
                this.selectedIndex = this.imgs.indexOf(item);
            }
        },
        deleteImg(item) {
            if ((this.selectedIndex == this.imgs.length - 1) && this.selectedIndex != 0) {
                this.decrementSelectedIndex();                
            }
            this.imgs.splice(this.imgs.indexOf(item), 1);
            this.underMouse = -1;
        },
        decrementSelectedIndex() {
            this.selectedIndex--;
            if (this.selectedIndex <= ((this.selectedGroup-1)*this.imgsInGroup-1)) {
                this.selectedGroup--;
            }
        },
        incrementSelectedIndex() {
            this.selectedIndex++;
            if (this.selectedIndex > (this.selectedGroup*this.imgsInGroup-1)) {
                this.selectedGroup++;
            }
        },
        changeUnderMouse(item, index) {
            if (item) {
                this.underMouse = index;
            }
        },
        changeSelectedGroup(value) {
            switch (value) {
                case -1: {
                    if (this.selectedGroup > 1) {
                        this.selectedGroup--;
                        this.selectedIndex = (this.selectedGroup - 1)  * this.imgsInGroup;
                    }
                    break;
                }
                case 1: {
                    if (this.selectedGroup < this.groupCount) {
                        this.selectedGroup++;
                        this.selectedIndex = (this.selectedGroup - 1)  * this.imgsInGroup;
                    }
                    break;
                }
            }
        }
    }
}
</script>

<style>
.gallery {
    display: flex;
    flex-direction: column;
    align-items: center;
}
.container {
    width: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
}
.current-img-wrapper {
    width: 725px;
    height: 363px;          
    overflow: hidden; 
    display: flex;
    justify-content: center;
    align-items: center; 
    margin: 0 8px 10px;
}
.current-img {
    width: auto;
    height: 100%;
}
.arrow-btn {
    border: none;
    background-color: transparent;
    cursor: pointer;
    padding: 0;
}
.arrow-btn:disabled {
    cursor: auto;
    opacity: 0.5;
}
.arrow-img {
    width: 23px;
}
.imgs-box {
    width: 725px;
    display: flex;
    align-items: center;
    justify-content: space-between;    
}
.gallery-img-wrapper {
    width: 70px;
    height: 70px;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    position: relative;
}
.gallery-img {
    height: 100%;
    width: auto;
}
.gallery-img-shadow {
    content: '';
    display: block;
    position: absolute;
    width: 100%;
    height: 100%;
    background-color: rgba(80, 80, 80, 0.5);
    top: 0;
    left: 0;
}
.delete-ico-wrapper {
    position: absolute;
    width: 100%;
    height: 20px;
    bottom: 0;    
    background-color: rgba(80, 80, 80, 0.5);
}
.delete-ico {
    position: absolute;
    height: 16px;
    bottom: 2px;
    right: 2px;
}
.groups {
    margin: 10px 0;
}
.group-counter {
    font-size: 28px;
    margin: 0 42px;
}
#upload-img {
    display: none;
}
.upload-btn {
    color: white;
    background-color: #202020;
    border: none;
    border-radius: 17px;
    width: 200px;
    height: 35px;
    font-size: 16px;
    margin: 0 12px;    
}
.upload-btn:hover {
    background-color: #444444;
}
.upload-btn:active {
    background-color: #090909;
}
</style>