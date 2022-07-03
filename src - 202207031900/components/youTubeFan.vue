<template>
  <div>
    <div id="00010">
      <!--スクロールすると戻るボタン表示-->
      <v-btn v-scroll="onScroll" v-show="fab" fab dark fixed bottom right color="primary" @click="toTop">
        <v-icon>mdi-chevron-up</v-icon>
      </v-btn>
    </div>
    <!-- ############## container dialog ############## -->
    <div id="00020">
      <v-container><!--Gridの入れ物-->
        <!--#### 行 1/1行目 ####-->
        <v-footer padless>
          <v-row justify="center">
            <v-toolbar dense>
              <v-spacer></v-spacer>
              <!-- +++++ Dialog1 dialogSet  +++++++++++++++++++++ -->
              <v-dialog v-model="dialogSet" width="500px">
                <template v-slot:activator="{ on, attrs }">
                  <v-btn width="120px" class="ma-2" outlined color="indigo" v-bind="attrs" v-on="on">
                    <v-icon>mdi-checkbox-marked-circle</v-icon>
                    Search
                  </v-btn>
                </template>
                <v-card elevation="24" width="480" class="pa-1 ma-2">
                  <v-card-title>
                    <span class="text-h5">Display customization</span>
                  </v-card-title>
                  <v-card-text><small>*indicates required field</small></v-card-text>
                  <v-card-text>
                    <v-container><!--Gridの入れ物-->
                      <v-row> <!--1行目-->
                        <small><u>Select display mode</u></small>
                      </v-row><!--1行目-->
                      <v-row> <!--2行目-->
                        <v-col cols="12" sm="6">
                          <v-select v-model="selDspOrdType"
                          :items="listTypes" item-text="name" item-value="id"
                          label="Display order*" required></v-select>
                        </v-col>
                        <v-col cols="12" sm="6">
                          <v-select v-model="selDspImgType"
                          :items="imgTypes" item-text="name" item-value="id"
                          label="Display image*" required></v-select>
                        </v-col>
                      </v-row><!--2行目-->
                      <v-row> <!--3行目-->
                        <small><u>Set extraction options</u></small>
                      </v-row><!--3行目-->
                      <v-row> <!--4行目--><!--https://vuetifyjs.com/ja/components/text-fields/-->
                        <v-col cols="12" sm="12">
                          <v-text-field v-model="searchWords"
                            counter="25"
                            hint="(Blank is delimiter for ANDserch)"
                            label="Search..."
                          ></v-text-field>
                        </v-col>
                      </v-row><!--4行目-->
                      <v-row> <!--5行目--><!--https://vuetifyjs.com/ja/components/date-pickers/-->
                        <v-col cols="12" sm="6">
                          <v-menu v-model="menuOfS"
                            :close-on-content-click="false"
                            :nudge-right="40"
                            transition="scale-transition" offset-y min-width="auto">
                            <template v-slot:activator="{ on, attrs }">
                              <v-text-field v-model="dateOfS" label="start day"
                                prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on">
                              </v-text-field>
                            </template>
                            <v-date-picker v-model="dateOfS" @input="menuOfS = false"></v-date-picker>
                          </v-menu>
                          <v-row  justify="end">
                            <v-btn color="green darken-1<" text @click="clrDateOfS"><small>clear</small></v-btn>
                          </v-row>
                        </v-col>
                        <v-col cols="12" sm="6">
                          <v-menu v-model="menuOfE"
                            :close-on-content-click="false"
                            :nudge-right="40"
                            transition="scale-transition" offset-y min-width="auto">
                            <template v-slot:activator="{ on, attrs }">
                              <v-text-field v-model="dateOfE" label="end day"
                                prepend-icon="mdi-calendar" readonly v-bind="attrs" v-on="on">
                              </v-text-field>
                            </template>
                            <v-date-picker v-model="dateOfE" @input="menuOfE = false"></v-date-picker>
                          </v-menu>
                          <v-row  justify="end">
                            <v-btn color="green darken-1" text @click="clrDateOfE"><small>clear</small></v-btn>
                          </v-row>
                        </v-col>
                      </v-row><!--5行目-->
                      </v-container>
                  </v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="dialogSet = false">Close</v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
              <!-- +++++ Dialog1 dialogSet  +++++++++++++++++++++ -->
              <!--#### 行 5行目 ####-->
              <template>
                <!--<v-btn color="indigo" text><span style="font-size: 12px;">PLAYER</span></v-btn>-->
                <template v-if="youtubeApiReady"> <!--◎-->
                  <template v-if="playerEnable">  <!--○-->
                    <v-btn width="120px" class="ma-2" outlined color="indigo" 
                      v-bind:disabled="playerClsFlag" text @click="closePlayer">
                      <v-icon>mdi-checkbox-marked-circle</v-icon>
                      CLOSE
                    </v-btn>
                  </template>                     <!--○-->
                  <template v-else>               <!--○-->
                    <v-btn width="120px" class="ma-2" outlined color="indigo" 
                      v-bind:disabled="playerRstFlag" text @click="restartPlayer">
                      <v-icon>mdi-checkbox-marked-circle</v-icon>
                      REPLAY
                    </v-btn>
                  </template>                     <!--○-->
                </template>                       <!--◎-->
                <template v-else>                 <!--◎-->
                  <v-btn width="120px" class="ma-2" outlined color="indigo" 
                    v-bind:disabled="playerOpnFlag" text @click="execPlayer">
                    <v-icon>mdi-checkbox-marked-circle</v-icon>
                    PLAY
                  </v-btn>
                </template>                       <!--◎-->
              </template>
              <!-- +++++ Dialog2 dialogNote ++++++++++++++++++++ -->
              <v-dialog v-model="dialogNote" width="500px">
                <template v-slot:activator="{ on, attrs }">
                  <v-btn width="120px" class="ma-2" outlined color="indigo" v-bind="attrs" v-on="on">
                    <v-icon>mdi-checkbox-marked-circle</v-icon>
                    Note
                  </v-btn>
                </template>
                <v-card elevation="24" width="480" class="pa-1 ma-2">
                  <v-card-title>
                    <span class="text-h5">Note</span>
                  </v-card-title>
                  <v-card-text>
                    <ul>
                      <li>Wi-Fi環境で使用してください<br>Please use in a Wi-Fi environment</li>
                      <li>データは{{mydataDay}}に収集されました<br>The data was collected on {{mydataDay}}</li>
                    </ul>
                    <template  v-if="dialogDebug">
                      <br>
                      <!--#### 行 7行目 ####-->
                      <v-row justify="start">
                        <v-btn text><span style="font-size: 9px;">Number of extracted videos:{{this.videoInfos.length}} / Total number:{{this.videoInfosOrg.length}}</span></v-btn>
                        <v-spacer></v-spacer> 
                      </v-row>
                      <!--#### 行 8行目 ####-->
                      <v-row justify="center">
                        <!--内部情報表示用域-->
                        <!--<v-divider></v-divider>-->
                      </v-row>
                    </template>
                    <template  v-if="dialogDebug">
                      <br>
                      -- debug infomation --<br> 
                      playerEnable            : {{playerEnable}}<br>
                      youtubeApiReady         : {{youtubeApiReady}}<br>
                      this.videoInfosOrg.length  : {{this.videoInfosOrg.length}}<br>
                      this.videoInfos.length     : {{this.videoInfos.length}}<br>
                      this.videoInfosDisp.length : {{this.videoInfosDisp.length}}<br>
                    </template>
                  </v-card-text>
                  <v-card-actions>
                    <v-btn color="rgba(255,255,255,0)" text @click="dialogDebug = true">Debug</v-btn>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="dialogNote = false;dialogDebug = false">Close</v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
              <!-- +++++ Dialog2 dialogNote ++++++++++++++++++++ -->
              <v-spacer></v-spacer>
            </v-toolbar>
          </v-row>
        </v-footer>
      </v-container>
    </div>
    l<!------------------------------------------------------------------------------->
    <!-- ############## container ############## -->
    <div id="00030">
      <v-container><!--Gridの入れ物-->
        <template> <!--●-->
          <!--#### 行 1行目 ####-->
          <v-row justify="start">
            <div  style="margin-left: 20px;" >
              Favorite videos ({{myChannel}})
            </div>
          </v-row>          
          <!--#### 行 2行目 ####-->
          <v-row justify="center">
            <v-divider></v-divider>
          </v-row>
          <!--#### 行 3行目 ####-->
          <v-row justify="center">
            <!--<v-btn color="indigo" text>Select</v-btn>-->
            <v-btn v-bind:disabled="allSetFlag" text @click="allSet">AllCHK</v-btn>
            <v-btn v-bind:disabled="allClrFlag" text @click="allClr">AllCLR</v-btn>
            <v-spacer></v-spacer>
            <v-btn v-bind:disabled="execBtnPrevFlag" text @click="execBtnPrev"><v-icon>mdi-chevron-left</v-icon>prev</v-btn>
            <v-spacer></v-spacer>
            <v-btn v-bind:disabled="execBtnNextFlag" text @click="execBtnNext">next<v-icon>mdi-chevron-right</v-icon></v-btn>
            <v-spacer></v-spacer> 
            <v-btn text>{{getPageCnt(videoNoStart,"type1")}} / {{getPageCnt(this.videoInfos.length,"type2")}} / {{getPageCnt(this.videoInfosOrg.length,"type3")}}</v-btn>
          </v-row>
          <!--#### 行 4行目 ####-->
          <v-row justify="center">
            <v-divider></v-divider>
          </v-row>
          <!--#### 行 5行目 ####-->
        </template> <!--●-->
      </v-container>
    </div>
    <!-- ############## container ############## -->
    <!------------------------------------------------------------------------------->
    <div id="00040">
      <template>
        <v-row justify="start">
          <div class="ml-4">

            <template v-if="message != ''">
              <br>
              <div style="margin-left: 20px;" v-html="message"></div>
              <br>              
            </template>
          </div>
        </v-row>
      </template>
    </div>
    <div id="00050">
      <template>   <!--<template v-if="playerEnable">-->
        <v-row justify="start">
          <div id="player" style="margin-left: 20px;"></div>
        </v-row>
        <v-row justify="center">
          <!--<v-btn class="ma-2 white--text" color="blue-grey" @click="exitPlayer">exit</v-btn>-->
        </v-row>
      </template> <!--"playerEnable" -->
    </div>
    <div id="00060">
      <template>
        <!--<template v-else> --><!--not "playerEnable" -->
        <!-- ############## container ############## -->
        <v-container><!--Gridの入れ物-->
          <!--#### 行 1行目 ####-->
          <v-row justify="start">
            <template v-if="playerEnable">
              <br>
            </template>
          </v-row>
          <!--#### 行 2行目 ####-->
          <v-row justify="start">
            <!-- ####  v-for="videoInfo in headOfvideoInfos #### -->
            <div class="content_movie" v-for="videoInfo in headOfvideoInfos" :key="videoInfo.videoId">
              <!-- #### v-card ###################### -->
              <v-card dark elevation="24" width="480" class="pa-1 ma-2">
                <div v-bind:id="'#ID520_VID_' + videoInfo.videoId">
                  <v-card-title>
                    <v-checkbox v-model="checkboxchecked"
                      v-bind:label="videoInfo.videoId"
                      v-bind:value="videoInfo.videoId">
                      <template v-slot:label>
                        <div>{{ videoInfo.title }}</div>
                      </template>
                    </v-checkbox>
                  </v-card-title>
                  <v-card-text>
                    (URL: <a v-bind:href="'https://youtu.be/' + videoInfo.videoId" target="_blank">
                      "https://youtu.be/{{ videoInfo.videoId }}"</a> )
                  </v-card-text>
                  <!-- ##  v-if="selDspImgType ## -->
                  <template v-if="selDspImgType === 'dspType1'"> <!-- dspType1:動画 -->
                    <div class="video">
                      <iframe width="461" height="259" 
                        v-bind:src="'https://www.youtube.com/embed/' + videoInfo.videoId" 
                        title="YouTube video player" 
                        frameborder="0" 
                        allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
                        allowfullscreen>
                      </iframe>
                    </div>
                  </template> <!-- dspType1:動画 -->
                  <!-- ##  v-if="selDspImgType ## -->
                  <template v-else-if="selDspImgType === 'dspType2'"> <!-- dspType2:サムネイル(大) -->
                    <div class="video">
                      <a v-bind:href="'https://youtu.be/' + videoInfo.videoId" target="_blank">
                        <object class="thumnail" v-bind:data="videoInfo.thumbnailsm">
                        </object>
                      </a>
                    </div>
                  </template> <!-- dspType2:サムネイル(大) -->
                  <!-- ##  v-if="selDspImgType ## -->
                  <template v-else-if="selDspImgType === 'dspType3'"> <!-- dspType3:サムネイル(小) -->
                    <div class="video">
                      <a v-bind:href="'https://youtu.be/' + videoInfo.videoId" target="_blank">
                        <object class="thumnail" v-bind:data="videoInfo.thumbnailsd">
                        </object>
                      </a>
                    </div>
                  </template> <!-- dspType3:サムネイル(小) -->
                  <!-- ##  v-if="selDspImgType ## -->
                  <template v-else>
                  </template>
                  <template><!--https://vuetifyjs.com/ja/components/data-tables/-->
                    <v-data-table
                      dense
                      hide-default-footer
                      :headers="headers"
                      :items="[{
                        view:videoInfo.viewCount,
                        good:videoInfo.likeCount,
                        comment:videoInfo.commentCount,
                        duration:getDuration(videoInfo.duration),
                        publishedAt:gePublished(videoInfo.publishedAt,'yyyymmdd')
                      }]"
                    ></v-data-table>
                  </template>
                </div><!--video id-->
              </v-card>
              <v-divider></v-divider>
              <!--#### 行 3行目 ####-->
            </div>
            <!-- ####  v-for="videoInfo in headOfvideoInfos #### -->
          </v-row>
          <!--#### 行 3行目 ####-->
        </v-container>
        <!-- ############## container ############## -->
      </template> <!--not "playerEnable" -->
    </div>
    <!------------------------------------------------------------------------------->
  </div>
</template>

<script>
  export default {
    name: 'youTubeFan',

    data: () => ({
    //--------------------------------------------------
    videoInfosOrg: 
    [{"videoId": "s-7NlXFvD6Q", "thumbnailsd": "https://i.ytimg.com/vi/s-7NlXFvD6Q/default.jpg", "thumbnailsm": "https://i.ytimg.com/vi/s-7NlXFvD6Q/mqdefault.jpg", "publishedAt": "2019-12-29T05:40:26Z", "duration": "PT4M37S", "viewCount": "2612", "favoriteCount": "0", "likeCount": "6", "dislikeCount": "", "commentCount": "0", "channelId": "UCLozvOV1p2Ys-Z6jKKQL-KQ", "channelTitle": "WEBテストの玉手箱 お役立ち道具", "title": "01.四則逆算電卓（1）"}, {"videoId": "pJkebv2qe2Q", "thumbnailsd": "https://i.ytimg.com/vi/pJkebv2qe2Q/default.jpg", "thumbnailsm": "https://i.ytimg.com/vi/pJkebv2qe2Q/mqdefault.jpg", "publishedAt": "2019-12-29T07:45:49Z", "duration": "PT1H55S", "viewCount": "289", "favoriteCount": "0", "likeCount": "0", "dislikeCount": "", "commentCount": "0", "channelId": "UCLozvOV1p2Ys-Z6jKKQL-KQ", "channelTitle": "WEBテストの玉手箱 お役立ち道具", "title": "02.四則逆算電卓 コピー＆ペーストで式入力"}, {"videoId": "7HuAqg4xjgI", "thumbnailsd": "https://i.ytimg.com/vi/7HuAqg4xjgI/default.jpg", "thumbnailsm": "https://i.ytimg.com/vi/7HuAqg4xjgI/mqdefault.jpg", "publishedAt": "2019-12-29T07:50:40Z", "duration": "PT3M", "viewCount": "623", "favoriteCount": "0", "likeCount": "0", "dislikeCount": "", "commentCount": "0", "channelId": "UCLozvOV1p2Ys-Z6jKKQL-KQ", "channelTitle": "WEBテストの玉手箱 お役立ち道具", "title": "03.四則逆算電卓　キーボード入力"}, {"videoId": "rE-ZXwrBnJ8", "thumbnailsd": "https://i.ytimg.com/vi/rE-ZXwrBnJ8/default.jpg", "thumbnailsm": "https://i.ytimg.com/vi/rE-ZXwrBnJ8/mqdefault.jpg", "publishedAt": "2020-01-03T04:37:21Z", "duration": "PT1H2M3S", "viewCount": "483", "favoriteCount": "0", "likeCount": "1", "dislikeCount": "", "commentCount": "0", "channelId": "UCLozvOV1p2Ys-Z6jKKQL-KQ", "channelTitle": "WEBテストの玉手箱 お役立ち道具", "title": "04.四則逆算　デモ"}, {"videoId": "V40VPhwQ8RI", "thumbnailsd": "https://i.ytimg.com/vi/V40VPhwQ8RI/default.jpg", "thumbnailsm": "https://i.ytimg.com/vi/V40VPhwQ8RI/mqdefault.jpg", "publishedAt": "2020-01-03T04:39:40Z", "duration": "PT12H34M56S", "viewCount": "184", "favoriteCount": "0", "likeCount": "0", "dislikeCount": "", "commentCount": "0", "channelId": "UCLozvOV1p2Ys-Z6jKKQL-KQ", "channelTitle": "WEBテストの玉手箱 お役立ち道具", "title": "05.四則逆算 カスタマイズ 確認画面非表示"}, {"videoId": "pMyj4BVx92U", "thumbnailsd": "https://i.ytimg.com/vi/pMyj4BVx92U/default.jpg", "thumbnailsm": "https://i.ytimg.com/vi/pMyj4BVx92U/mqdefault.jpg", "publishedAt": "2022-05-21T04:40:50Z", "duration": "PT18S", "viewCount": "97", "favoriteCount": "0", "likeCount": "0", "dislikeCount": "", "commentCount": "0", "channelId": "UCLozvOV1p2Ys-Z6jKKQL-KQ", "channelTitle": "WEBテストの玉手箱 お役立ち道具", "title": "06.四則逆算 カスタマイズ 文字置換"}, {"videoId": "k3XZML8cAj0", "thumbnailsd": "https://i.ytimg.com/vi/k3XZML8cAj0/default.jpg", "thumbnailsm": "https://i.ytimg.com/vi/k3XZML8cAj0/mqdefault.jpg", "publishedAt": "2022-05-22T15:19:26Z", "duration": "PT1M17S", "viewCount": "181", "favoriteCount": "0", "likeCount": "5", "dislikeCount": "", "commentCount": "0", "channelId": "UCLozvOV1p2Ys-Z6jKKQL-KQ", "channelTitle": "WEBテストの玉手箱 お役立ち道具", "title": "07.WEB テスト解答集検索ツール"}, {"videoId": "ltocm7WzQ2c", "thumbnailsd": "https://i.ytimg.com/vi/ltocm7WzQ2c/default.jpg", "thumbnailsm": "https://i.ytimg.com/vi/ltocm7WzQ2c/mqdefault.jpg", "publishedAt": "2022-05-23T14:04:23Z", "duration": "PT1M1S", "viewCount": "25", "favoriteCount": "0", "likeCount": "1", "dislikeCount": "", "commentCount": "0", "channelId": "UCLozvOV1p2Ys-Z6jKKQL-KQ", "channelTitle": "WEBテストの玉手箱 お役立ち道具", "title": "08.「WEBテスト解答集検索ツール」使う前（Excelで検索）"}, {"videoId": "ZS3mVd02ZO4", "thumbnailsd": "https://i.ytimg.com/vi/ZS3mVd02ZO4/default.jpg", "thumbnailsm": "https://i.ytimg.com/vi/ZS3mVd02ZO4/mqdefault.jpg", "publishedAt": "2022-05-24T14:06:39Z", "duration": "PT23S", "viewCount": "43", "favoriteCount": "0", "likeCount": "0", "dislikeCount": "", "commentCount": "3", "channelId": "UCLozvOV1p2Ys-Z6jKKQL-KQ", "channelTitle": "WEBテストの玉手箱 お役立ち道具", "title": "09.「WEBテスト解答集検索ツール」使った場合"}, {"videoId": "64DlYpwDMPM", "thumbnailsd": "https://i.ytimg.com/vi/64DlYpwDMPM/default.jpg", "thumbnailsm": "https://i.ytimg.com/vi/64DlYpwDMPM/mqdefault.jpg", "publishedAt": "2022-05-25T12:54:24Z", "duration": "PT27S", "viewCount": "14", "favoriteCount": "0", "likeCount": "0", "dislikeCount": "", "commentCount": "4", "channelId": "UCLozvOV1p2Ys-Z6jKKQL-KQ", "channelTitle": "WEBテストの玉手箱 お役立ち道具", "title": "10.Webテスト解答集の冗長データ確認"}, {"videoId": "sPFsWDOik4o", "thumbnailsd": "https://i.ytimg.com/vi/sPFsWDOik4o/default.jpg", "thumbnailsm": "https://i.ytimg.com/vi/sPFsWDOik4o/mqdefault.jpg", "publishedAt": "2022-05-26T13:03:28Z", "duration": "PT49S", "viewCount": "13", "favoriteCount": "0", "likeCount": "0", "dislikeCount": "", "commentCount": "4", "channelId": "UCLozvOV1p2Ys-Z6jKKQL-KQ", "channelTitle": "WEBテストの玉手箱 お役立ち道具", "title": "11.玉手箱 図表の読み取り の問題を解いてみる"}, {"videoId": "MqfwJSk5sTs", "thumbnailsd": "https://i.ytimg.com/vi/MqfwJSk5sTs/default.jpg", "thumbnailsm": "https://i.ytimg.com/vi/MqfwJSk5sTs/mqdefault.jpg", "publishedAt": "2022-05-27T16:41:26Z", "duration": "PT59S", "viewCount": "10", "favoriteCount": "0", "likeCount": "0", "dislikeCount": "", "commentCount": "5", "channelId": "UCLozvOV1p2Ys-Z6jKKQL-KQ", "channelTitle": "WEBテストの玉手箱 お役立ち道具", "title": "12.「玉手箱」-「図表読み取り」電卓で、「Ａを100とした時のＢは？」を計算してみた。"}]
    ,
    //Title
    myChannel : 'マイチャンネル',
    //Note
    mydataDay : '2022/06/10',
    //--------------------------------------------------
    videoInfos: null,          //検索文字や期間で絞り込まれた情報
    videoInfosDisp: null,      //videoInfosの中で現画面に表示されている情報
    //--------------------------------------------------
    //スクロールすると戻るボタン表示
    fab: false,
    //--------------------------------------------------
    //debug
    loglevel  : 5,
    //--------------------------------------------------
    //dialog
    checkInputOk     : false,  //設定が正しいとtrueに変化
    dialogPlay       : false,  //初期表示 非表示(false)
    dialogSet        : false,  //初期表示 非表示(false)
    dialogNote       : false,  //初期表示 非表示(false)
    dialogDebug      : false,  //初期表示 非表示(false)

    selDspOrdType    : null ,    //Display order
    listTypes: [
      {id:'listType11',name:'Oldest is first'}, 
      {id:'listType12',name:'Newest is first'}, 
      {id:'listType01',name:'view count'     }, 
      {id:'listType02',name:'good count'     }, 
      {id:'listType03',name:'comment count'  },
    ],
    selDspImgType    : null,     //Display image
    imgTypes: [
      {id:'dspType1',name:'Video'       },
      {id:'dspType2',name:'thumbnail(L)'},
      {id:'dspType3',name:'thumbnail(S)'},
      {id:'dspType4',name:'none'        }, 
    ],

    selPriodDays     : null,   //start day
    //selPriodDaysText : null, //computed:で定義

    searchWords      : null,   //絞り込み単語
    cntVideoChnl     : null,   //表示件数 全

    //--------------------------------------------------
    //control 制御用
    typeCntPerPg : null,   //0:Video,1:thumbnail(L),2:thumbnail(S),3:none
    videoPerPage : [1,4,8,16],   //頁あたり

    videoNoStart : null,   //表示開始番号
    videoNoEndP1 : null,   //表示終了番号+1(この番号は含まれない)

    sort_key     : "",
    sort_asc     : null,   //asc:昇順,desc:降順

    dateOfS  : null,
    dateOfE  : null,
    menuOfS  : false,      //開時true
    menuOfE  : false,      //開時true

    headers: [
      {value:'view', text:'view'},
      {value:'good', text:'good'},
      {value:'comment', text:'comment'},
      {value:'duration', text:'duration'},
      {value:'publishedAt', text:'publishedAt'}
    ],
    //- youtube playlist -------------------------------
    myPlayListId    : null,    //videoidを,で区切った文字列。1個以上の設定で"PLAY"押下可能
    myPlayListTitle : null,    //videoidに対応するタイトル文字列。plat.html起動時に引数として渡す。現在、plat.html起動は未使用。
    checkboxchecked : [],      //選択されたvideoidは配列として格納される。 
    //--------------------------------------------------
    playerEnable    : false,  //意味：HTMLで動画表示画面を表示するかどうか
                              //execPlayer()              => playerEnableをtrueに
    youtubeApiReady : false,  //意味："https://www.youtube.com/iframe_api"が呼び出されたか？
        //1. execPlayer()->initYoutube()にて、
        //   iframe_apiから、window.onYouTubeIframeAPIReady()が呼び出され、本フラグがtrueになる。
        //2. 本フラグを監視し、trueになれば、l_onYouTubeIframeAPIReady()が呼び出される。
        //trueになってからfalseになることはない。
    player          : null,   //意味：youtubeオブジェクト
    playFirstdone   : false,  //意味：動画開始の初回だけ停止するための判断に使用
    //--------------------------------------------------
    }),
    //==================================================
    created : function(){  //DOMは未作成
      this.lconsolelog(5,'created');
      this.copyVideList();
    },
    //==================================================
    mounted : function(){  //DOMは作成済
      this.lconsolelog(5,'mounted')
      //-------------------------------
      this.selDspOrdType = 'listType01';
      this.selDspImgType = 'dspType4';
      this.dialogSetInit();
    },
    //==================================================
    watch: {
      //--------------------------------------------------
      dialogSet : function(newValue, old){
        this.lconsolelog(5,'dialogSet:' + old + '=>' + newValue)
        if(!newValue){  //false(閉じられた)
          this.dialogSetInit();
        }
      },
      //--------------------------------------------------
      //dialogPlay : function(newValue, old){
      //  this.lconsolelog(5,'dialogPlay:' + old + '=>' + newValue)
      //  if(newValue){;}  //true(開かれた)
      //},
      //--------------------------------------------------
      checkboxchecked : function(newValue, old){
        this.lconsolelog(5,"checkboxchecked:" + old + "=>" + newValue);
        this.myPlayListId = newValue.join(',');   //videoidの配列->文字列
        //videoidに対応するtitleを現在表示中から捜す
        let str = "";
        for (const id of newValue) {
          const tgtlist = this.videoInfos.find((list) => list.videoId === id);
          str = str + tgtlist.title + ",";
        }//複数頁でチェックした場合、ideoInfosDispではNG
        if (str.length > 0) {
          str = str.slice(0,-1);  //末尾の","削除
        }
        this.myPlayListTitle = str;
      },
      //--------------------------------------------------
      youtubeApiReady: function(newValue, old){
        this.lconsolelog(5,'youtubeReady : ' + old + "=>" + newValue)
        if (newValue == true) {
          this.l_onYouTubeIframeAPIReady();
        }
      },
      //--------------------------------------------------
    },
    //==================================================
    computed: {
      //--------------------------------------------------
      // Dialog
      //--------------------------------------------------
      //dateOfE() {
      //  return
      //},
      //--------------------------------------------------
      // Head
      //--------------------------------------------------
      execBtnNextFlag: function() {
        let f = true;
        if (this.checkInputOk == true) {
          if ((this.videoNoStart + this.videoPerPage[this.typeCntPerPg]) < this.videoInfos.length) {
            f = false;  //次頁が存在するなら有効
          }
        }
        return f;
      },
      //--------------------------------------------------
      execBtnPrevFlag: function() {
        let f = true;
        if (this.checkInputOk == true) {
          if ((this.videoNoStart - this.videoPerPage[this.typeCntPerPg]) >= 0) {
            f = false;  //前頁が存在するなら有効
          }
        }
        return f;
      },
      //--------------------------------------------------
      playerOpnFlag: function() {
        let f = true;
        if ((this.checkInputOk == true) 
        && (this.youtubeApiReady == false)
        && (this.checkboxchecked.length > 0)) {
          f = false;
        }
        return f;
      },
      //--------------------------------------------------
      playerRstFlag: function() {
        let f = true;
        if ((this.checkInputOk == true) 
        && (this.youtubeApiReady == true)
        && (this.checkboxchecked.length > 0)) {
          f = false;
        }
        return f;
      },
      //--------------------------------------------------
      playerClsFlag: function() {
        let f = true;
        if ((this.checkInputOk == true) && (this.playerEnable == true)) {
          f = false;
        }
        return f;
      },
      //--------------------------------------------------
      allSetFlag: function() {
        let f = true;
        if (this.checkInputOk == true) {
          f = false;
        }
        return f;
      },
      //--------------------------------------------------
      allClrFlag: function() {
        let f = true;
        if (this.checkInputOk == true) {
          f = false;
        }
        return f;
      },
      //--------------------------------------------------
      message: function() {
        let strs = "" //"<p><b><font color=\"red\">";
        let str = ""
        let stre = "" //"</font></b></p>";
        if (this.checkInputOk == false) {
          str = strs + 'Click "SEARCH"(above).' + stre;
        } else if(this.videoInfos.length == 0) {
          str = strs + 'Click "SEARCH"(above) and Review search conditions.' + stre;
        } else if(this.playerEnable) {
          str = str + '<ul>';
          str = str + '<li>(case:start)Click ▶ in video. (and mute off in video.)</li>';
          str = str + '<li>(case:stop )Click "CLOSE"(above).</li>';
          str = str + '</ul>';
        } else if(this.videoInfosDisp.length > 0) {
          str = str + '<ul>';
          str = str + '<li>(case:show one video)Click url or image.</li>';
          str = str + '<li>(case:show playlist)Check some checkboxes and Click "PLAY"(above).</li>';
          str = str + '</ul>';
          str = strs + str + stre;
        }
        return str;
      },
      //--------------------------------------------------
      getPlayerUrl: function() {
        let str = "";
        if (this.checkInputOk == true) {
          str = './playlist.html'
          str = str + '?'
          str = str + 'videolistid='
          str = str + 's-7NlXFvD6Q'
          str = str + '&'
          str = str + 'videolisttitle='
          str = str + 's-7NlXFvD6Q'
          str = str + ' target=_blank'
        }
        return str;
      },
      //--------------------------------------------------
      // Table
      //--------------------------------------------------
      getPageCnt: function() {
        return function(vol,type) {
          let v = 0;
          if (this.checkInputOk == true) {
            let p = this.videoPerPage[this.typeCntPerPg]
            v = Math.floor((vol + p - 1)/ p);
            this.lconsolelog(4,'getPageCnt('+ type + ':(' + vol + '+' + p + '-1)/' + p + '=>' + v);
            if (vol == 0) {//(vol + p - 1)は、p==1の時0になる。p値に影響されないように、計算結果でなく、入力値で判断するようにする。
              switch(type) {
                case 'type1': //{{getPageCnt(videoNoStart,"type1")}} 0が開始番号
                  if (this.videoInfos.length > 0) {
                    v = 1;
                  } //detectCountが0なら0を返す。0/0/1
                  break;
                case 'type2': //{{getPageCnt(detectCount,"type2")}}
                  break;//検索された個数が0個 0/0/1
                case 'type3': //{{getPageCnt(videoInfos.length,"type3")}}
                  break;//0になることはない
              }
            }
          }//this.checkInputOk == false時、0/0/0
          return v;
        }
      },
      //--------------------------------------------------
      getDuration: function() {
        return function(duration) {
          let p,H,M,S;
          duration = duration.slice(2);  //先頭から2文字(PT)を削除
          p = duration.indexOf('H');     //Hの処理
          if (p == -1) { H = '0'; } 
          else { H = duration.substr(0,p); duration = duration.substr(p+1); }
          p = duration.indexOf('M');     //Mの処理
          if (p == -1) { M = '0'; } 
          else { M = duration.substr(0,p); duration = duration.substr(p+1); }
          p = duration.indexOf('S');     //Sの処理
          if (p == -1) { S = '0'; } 
          else { S = duration.substr(0,p); duration = duration.substr(p+1); }
          //return org + " => " + H + "h" + ('0'+M).slice(-2) + "m"  + ('0'+S).slice(-2) + "s"  + duration;
          return H + "h" + ('0'+M).slice(-2) + "m"  + ('0'+S).slice(-2) + "s"  + duration;
        }
      },
      //--------------------------------------------------
      gePublished: function() {
        return function(published,mode) {    //2019-12-29T05:40:26Z
          let p = published.indexOf('T');     //yyyy-mm-dd
          published = published.substr(0,p);
          if (mode != "yyyymmdd") {
            p = published.lastIndexOf('-');     //yyyy-mm
            published = published.substr(0,p);
            if (mode != "yyyymm") {
              p = published.lastIndexOf('-');     //yyyy
              published = published.substr(0,p);
            }
          }
          published = published.replace(/-/g,'/')
          return published;
        }
      },
      //--------------------------------------------------
      headOfvideoInfos: function () {
        //htmlのv-forから呼び出される
        //抽出
        this.lconsolelog(5,'headOfvideoInfos:s=' + this.videoNoStart + ' - e=' + this.videoNoEndP1);
        //return this.videoInfos
        //this.videoInfosDisp = this.videoInfos.slice(this.videoNoStart, this.videoNoEndP1);
        this.modyvideoInfosDisp(this.videoInfos.slice(this.videoNoStart, this.videoNoEndP1));
        return this.videoInfosDisp;
        //return this.videoInfos.slice(this.videoNoStart, this.videoNoEndP1);
        //DIALOGを閉じた時に削除せず、PREV NEXTで指定された範囲を返す。
      },
      //--------------------------------------------------
    },
    //==================================================
    methods: {
      lconsolelog(mode,msg) {
        if (mode >= this.loglevel) {
          console.log(msg);
        }
      },  
      //--------------------------------------------------
      copyVideList: function() {
          this.videoInfos = null;
          this.videoInfos = JSON.parse(JSON.stringify(this.videoInfosOrg));
          this.lconsolelog(5,'copyVideList(length):' + this.videoInfos.length);  
        },
      //--------------------------------------------------
      modyvideoInfosDisp: function(data) {
          this.videoInfosDisp = data;
        },
      //--------------------------------------------------
      chkdialogSet: function () {
        this.lconsolelog(5,'chkdialogSet');  
        let f = true;
        if (this.selDspOrdType == null) { f = false; }
        if (this.selDspImgType == null) { f = false; }
        this.checkInputOk = f;
        return f;
      },
      //--------------------------------------------------
      dialogSetInit: function () {
        this.lconsolelog(5,'dialogSetInit');  
        if (this.chkdialogSet() == true) {
          this.chkDialogInf();
          //
          this.copyVideList();     //videoListを再初期化
          this.sortVideoList();    //videoListを並べ替え
          this.serchVideoList();   //videoListから抽出
          this.limitVideoListatDay(); //期間で絞り込み
          //
          this.allClr();           //選択済checkbox clear
        }
        return;
      },
      //--------------------------------------------------
      clrDateOfE: function () {
        this.lconsolelog(5,'clrDateOfE');  
        this.dateOfE = null;
      },
      //--------------------------------------------------
      clrDateOfS: function () {
        this.lconsolelog(5,'clrDateOfS');  
        this.dateOfS = null;
      },
      //--------------------------------------------------
      chkDialogInf: function () {
        this.lconsolelog(5,'chkDialogInf');
        //selDspOrdType
        this.sort_asc = false;
        this.sort_key = "publishedAt";
        switch(this.selDspOrdType) {
          case 'listType11': //投稿順
            this.sort_asc = true;
            break;
          case 'listType12': //投稿順（新しい順）
            break;
          case 'listType01': //人気の動画（視聴回数順）
            this.sort_key = "viewCount";
            break;
          case 'listType02': //人気の動画（いいね数順）
            this.sort_key = "likeCount";
            break;
          case 'listType03': //人気の動画（コメント数順）
            this.sort_key = "commentCount";
            break;
        }
        switch(this.selDspImgType) {
          case 'dspType1': //Video
            this.typeCntPerPg = 0;
            break;
          case 'dspType2': //thumbnail(L)
            this.typeCntPerPg = 1;
            break;
          case 'dspType3': //thumbnail(S)
            this.typeCntPerPg = 2;
            break;
          case 'dspType4': //none
            this.typeCntPerPg = 3;
            break;
        }
        //video per page
        this.videoNoStart = 0;    //先頭から
        this.videoNoEndP1 = this.videoNoStart + this.videoPerPage[this.typeCntPerPg];
      },
      //--------------------------------------------------
      sortVideoList: function () {
        this.lconsolelog(5,'sortVideoList:sort_key='+this.sort_key);
        //Sort
        if (this.sort_key != "") {
          let rtn = 0
          let sign;
          this.sort_asc ? (sign = 1) : (sign = -1);
          this.videoInfos.sort((a,b) => {
            if (this.sort_key == 'publishedAt') {
              rtn = new Date(a[this.sort_key]) - new Date(b[this.sort_key]);
            } else {
              rtn = a[this.sort_key] - b[this.sort_key];  //数値を数値として扱うために減算
            }
          return rtn * sign;
          });
        }
      },
      //--------------------------------------------------
      zen2han2Up: function (str) {
        return str.toUpperCase().replace(/[Ａ-Ｚａ-ｚ０-９]/g, function(s) {
          return String.fromCharCode(s.charCodeAt(0) - 0xFEE0);
        });
      },
      //--------------------------------------------------
      serchVideoList: function () {
        this.lconsolelog(5,'serchVideoList');
        //-------------------------------------------
        if ((this.searchWords != null) && (this.searchWords.length != 0)){
          //入力検索文字列変換
          //sanitizing
          let str = this.searchWords;
          this.lconsolelog(5,"word:" + str);
          //sanitizing(html &<>"')
          str = str.replace(/&/g,'').replace(/</g,'').replace(/>/g,'').replace(/"/g,'').replace(/'/g,'');
          //全角空白を半角空白
          str = str.replace(/\u{3000}/g,' ');
          //入力検索文字列変換
          this.videoInfos = this.videoInfos.filter(
            video => {
              return this.isAllIncludes(this.zen2han2Up(video.title), this.zen2han2Up(str));
            }
          );
        }
      },
      //--------------------------------------------------
      isAllIncludes: function (title, words) {
        if (words == "") {
          return true;
        } else {
          let f = true;
          words.split(" ").forEach(function(word){
            if (f == true) {
              if (title.includes(word) == false) {
                f = false;
              }
            }
          })
          return f;
        }
      },
      //--------------------------------------------------
      limitVideoListatDay: function () {
        this.lconsolelog(5,'limitVideoListatDay');
        if ((this.dateOfS != null) || (this.dateOfE != null)) {
          //期間で絞り込み
          this.videoInfos = this.videoInfos.filter(
            video => {
              return this.isDayIncludes(
                this.gePublished(video.publishedAt,'yyyymmdd').replace(/\//g,'-'),
                this.dateOfS, this.dateOfE);
            }
            //publishedAtの形式：2020/01/03 => Date変換 => Fri Jan 03 2020 00:00:00 GMT+0900
            //dateOfS    の形式：2020-01-03 => Date変換 => Fri Jan 03 2020 09:00:00 GMT+0900
            //                                                             ~~~~~~~~
          );
        }
      },
      //--------------------------------------------------
      isDayIncludes: function (tday,sday, eday) {
        //console.log("tday=" + tday + ",sday=" + sday + ",eday=" + eday);
        let tdate = new Date(tday);
        let sdate;
        let edate;
        if (sday != null) { sdate = new Date(sday); }
        if (eday != null) { edate = new Date(eday); }

        let f = true;
        if ((sday == null) && (eday != null)) {                   //終了のみ
          if (tdate > edate) {
              f = false; 
            }                       //  終了より大きいものはfalse
        } else if ((sday != null) && (eday == null)) {            //開始のみ
          if (tdate < sdate) {
            //console.log("tdata=" + tdate + ",sdate=" + sdate);
            f = false; 
            }                       //  開始より小さいものはfalse
        } else if (sdate <= edate) {            //通常
          //console.log("tday=" + tday + ",sday=" + sday + ",eday=" + eday);
          if ((tdate > edate) || (tdate < sdate)) {
              f = false; 
            }  //  終了より大きいもの開始より小さいものはfalse
        } else if (sdate > edate) {             //区間を除く
          if ((tdate > edate) && (tdate < sdate)) {
              f = false; 
            }  //  終了より大きいもの開始より小さいものはfalse
        }
        if (f==true) {
          //console.log("sdate=>"+sdate+"\ntdate=>"+tdate+"\nedate=>"+edate)
        }
        return f;
      },
      //--------------------------------------------------
      execBtnNext: function () {
        this.lconsolelog(5,'execBtnNext');
        if ((this.videoNoStart + this.videoPerPage[this.typeCntPerPg]) < this.videoInfos.length) {
          //次頁が存在するなら更新
          this.videoNoStart = this.videoNoStart + this.videoPerPage[this.typeCntPerPg];
          this.videoNoEndP1 = this.videoNoStart + this.videoPerPage[this.typeCntPerPg]; 
        }
      },
      //--------------------------------------------------
      execBtnPrev: function () {
        this.lconsolelog(5,'execBtnPrev');
        if ((this.videoNoStart - this.videoPerPage[this.typeCntPerPg]) >= 0) {
          //前頁が存在するなら更新
          this.videoNoStart = this.videoNoStart - this.videoPerPage[this.typeCntPerPg];
          this.videoNoEndP1 = this.videoNoStart + this.videoPerPage[this.typeCntPerPg]; 
        }
      },
      //--------------------------------------------------
      allClr: function () {
        this.lconsolelog(5,'allClr');
        this.checkboxchecked = [];
      },
      //--------------------------------------------------
      allSet: function () {
        this.lconsolelog(5,'allSet');
        this.checkboxchecked = [];
        for(let elem of this.videoInfos) {
          this.checkboxchecked.push(elem.videoId);
        }
      },
      //-スクロールすると戻るボタン表示--------------------
      onScroll (e){
        if (typeof window === 'undefined') return
        const top = window.pageYOffset ||   e.target.scrollTop || 0
        this.fab = top > 500
      },
      toTop () {
        this.$vuetify.goTo(0)
      },
      //--------------------------------------------------
      execPlayer: function() {
        this.lconsolelog(5,'execPlayer');
        //url = './playlist.html?videolistid=' + this.myPlayListId;
        //url = url + '&videolisttitle=' + this.myPlayListTitle;
        //window.open(url, '_blank')

        this.playerEnable = true;     //youtube表示域
        this.initYoutube();
      },
      //--------------------------------------------------
      restartPlayer: function() {  //execPlayer()と統合できそうだが何度か失敗。別者とする。
        this.lconsolelog(5,'restartPlayer');
        this.playFirstdone = false;   //動画リスト開始時に停止させるため
        this.playerEnable = true;     //youtube表示域
        this.l_onYouTubeIframeAPIReady();
      },
      //--------------------------------------------------
      closePlayer: function() {
        this.lconsolelog(5,'closePlayer');
        this.stopVideo();
        //try
        this.player.destroy();
        this.playerEnable = false;    //youtube表示域
      },
      //--------------------------------------------------
      //--------------------------------------------------
      initYoutube: function() {
        this.lconsolelog(5,'initYoutube');

        var tag = window.document.createElement('script');
        tag.src = "https://www.youtube.com/iframe_api";
        var firstScriptTag = document.getElementsByTagName('script')[0];
        firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);
        this.lconsolelog(5,'initYoutube 2');
        window.onYouTubeIframeAPIReady = () => {
          this.lconsolelog(5,'YouTube API Ready');
          //onYouTubeIframeAPIReadyが呼び出されまでに時間がかかる。
          //onYouTubeIframeAPIReadyが実行されたのを確認してl_onYouTubeIframeAPIReadyを実行する。
          this.youtubeApiReady = true;
        };
      },
      //--------------------------------------------------
      l_onYouTubeIframeAPIReady: function() {  //←APIから起動される
        this.lconsolelog(5,'l_onYouTubeIframeAPIReady');
        this.player = new YT.Player('player', { // eslint-disable-line
          height: '360', width: '640',
          //height: '270', width: '480',
          //height: '180', width: '320',
          events: {
            'onReady': this.onPlayerReady,
            'onStateChange': this.onPlayerStateChange
          },
          playerVars: {
            playlist: this.myPlayListId //video_ids_id
            //playlist: 'ZS3mVd02ZO4,v=7ztzA-4vGtQ,MqfwJSk5sTs,ZS3mVd02ZO4'
            //NGplaylist: '[ZS3mVd02ZO4,v=7ztzA-4vGtQ,MqfwJSk5sTs,ZS3mVd02ZO4]'
          }
        });
        this.lconsolelog(5,typeof(this.player));
      },
      //--------------------------------------------------
      onPlayerReady: function(event) {
        this.lconsolelog(5,'onPlayerReady');
        event.target.mute();        //20220620 add
        event.target.playVideo();
      },
      
      //--------------------------------------------------
      onPlayerStateChange: function(event) {
        if (event.data == YT.PlayerState.PLAYING) {  // eslint-disable-line
          this.lconsolelog(5,'onPlayerStateChange PLAYING');
          if(!this.playFirstdone) {
            setTimeout(this.stopVideo, 0);  //0秒再生後停止、mute解除のため
            this.playFirstdone = true;
          }
        } else if (event.data == YT.PlayerState.ENDED) {  // eslint-disable-line
          this.lconsolelog(5,'onPlayerStateChange ENDED');
          this.allClr();           //選択済checkbox clear
        }
      },
      //--------------------------------------------------
      stopVideo: function() {
        this.player.stopVideo();
      },    
      //==================================================

    }
  }
</script>
