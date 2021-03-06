<template>
    <div class="assetMiniature" :class="[
        {'hasSettings': this.$props.config},
        {'isBackup': this.$props.status === 'backup'},
        {'isDisabled': isDisabled}
    ]">

        <div class="masterOverlay" v-if="isDisabled">
            <div class="overlayContentWrapper">
                <div class="spinnerWrapper">
                    <Spinner name="circle"/>
                </div>
                <div class="overlayMessage">Processing request</div>
            </div>

        </div>
        <div class="titleInfo">


            <div class="title" v-tooltip.top-start="assetName">
                <slot v-if="this.$props.status === 'backup'">BACKUP:</slot>
                {{assetName}}
            </div>


            <div class="dataWrapper">
                <div class="version" v-tooltip.top="`Asset size: ${assetSize}`" v-if="assetSize !== 'N/A'">
                    {{assetSize}}
                </div>
                <div class="version" v-tooltip.top="`Version: ${assetVersion}`" v-if="assetVersion.length > 0">
                    , {{assetVersion}}v
                </div>
            </div>

        </div>
        <div class="content">
            <slot v-if="hasPreview === true">
                <LightBox
                        class="previewLightbox"
                        :showLightBox="false"
                        ref="lightboxPreview"
                        :images="assetPreviewLightBoxImageSettings"
                        :showThumbs="false"
                ></LightBox>
            </slot>
            <div class="preview" v-tooltip.bottom-start="assetPreviewTitle" :class="[{hasPreview: hasPreview}]">

                <slot v-if="hasPreview === true">
                    <img :src="assetPreview" v-on:click="openPreview()">
                </slot>

                <slot v-if="hasPreview === false">
                    <img :src="assetPreview">
                </slot>

            </div>
            <div class="textDetails">

                <div class="detailBlock">
                    <div class="contactLine">
                        <div class="miniTitle">
                            <slot v-if="assetAuthorName !== assetDistributorName || assetAuthorName === 'N/A'">
                                Author:
                            </slot>

                            <slot v-if="assetAuthorName === assetDistributorName && assetDistributorName !== 'N/A'">
                                Author & distributor:
                            </slot>

                        </div>
                        <div class="contactIcons">

                            <div
                                    v-if="assetAuthorMail"
                                    v-tooltip.top="`Send author an e-mail: ${assetAuthorMail}`"
                                    v-on:click="openURL(assetAuthorMail)"
                                    class="icon sprite footer-mail">

                            </div>

                            <div
                                    v-if="assetAuthorUrl"
                                    v-tooltip.top="`Visit author's website: ${assetAuthorUrl}`"
                                    v-on:click="openURL(assetAuthorUrl)"
                                    class="icon sprite general-file-html">

                            </div>

                            <div
                                    v-if="assetAuthorDonationURL"
                                    v-tooltip.top="`Donate to the author: ${assetAuthorDonationURL}`"
                                    v-on:click="openURL(assetAuthorDonationURL)"
                                    class="icon sprite general-donate -positive">

                            </div>

                        </div>
                    </div>
                    <div class="textLine">
                        {{assetAuthorName}}
                    </div>
                </div>

                <div class="detailBlock"
                     v-if="assetAuthorName !== assetDistributorName || assetDistributorName === 'N/A'">
                    <div class="contactLine">
                        <div class="miniTitle">Distributor:</div>
                        <div class="contactIcons">

                            <div
                                    v-if="assetDistributorMail"
                                    v-tooltip.top="`Send distributor an e-mail: ${assetDistributorMail}`"
                                    v-on:click="openURL(assetDistributorMail)"
                                    class="icon sprite footer-mail">

                            </div>

                            <div
                                    v-if="assetDistributorUrl"
                                    v-tooltip.top="`Visit distributor's website: ${assetDistributorUrl}`"
                                    v-on:click="openURL(assetDistributorUrl)"
                                    class="icon sprite general-file-html">

                            </div>

                            <div
                                    v-if="assetDistributorDonationURL"
                                    v-tooltip.top="`Donate to the distributor: ${assetDistributorDonationURL}`"
                                    v-on:click="openURL(assetDistributorDonationURL)"
                                    class="icon sprite general-donate -positive">

                            </div>

                        </div>
                    </div>
                    <div class="textLine">
                        {{assetDistributorName}}
                    </div>
                </div>

                <div class="detailBlock">
                    <div class="contactLine">
                        <div class="miniTitle">
                            <slot v-if="hasCommentary === false">
                                License:
                            </slot>

                            <slot v-if="hasCommentary">
                                License & commentary:
                            </slot>

                        </div>
                        <div class="contactIcons">

                            <div
                                    v-if="assetLicenseURL"
                                    v-tooltip.top="`Read license online`"
                                    v-on:click="openURL(assetLicenseURL)"
                                    class="icon sprite general-file-html">

                            </div>

                            <div
                                    v-if="assetLicenseFile"
                                    v-tooltip.top="`Read license offline`"
                                    v-on:click="openURL(assetLicenseFile)"
                                    class="icon sprite general-file-html">

                            </div>

                            <div
                                    v-if="assetCommentaryFile"
                                    v-tooltip.top="`Read note on the license`"
                                    v-on:click="openURL(assetCommentaryFile)"
                                    class="icon sprite general-note-exists -important">

                            </div>

                        </div>
                    </div>
                    <div class="textLine">
                        {{assetLicense}}
                    </div>
                </div>

                <div class="detailBlock">
                    <div class="contactLine">
                        <div class="miniTitle">
                            Commercial use:
                        </div>
                        <div class="contactIcons">

                            <div
                                    v-if="assetCommercialURL"
                                    v-tooltip.top="`Obtain commercial license from the author`"
                                    v-on:click="openURL(assetCommercialURL)"
                                    class="icon sprite general-subscription-grey -positive">

                            </div>
                        </div>

                    </div>
                    <div class="textLine"
                         :class="[{'-positive': assetCommercialUse === 'Allowed'},{'-negative': assetCommercialUse !== 'Allowed'}]">
                        {{assetCommercialUse}}
                    </div>
                </div>

            </div>
        </div>

        <div class="detailBlock -tagline">
            <div class="contactLine">
                <div class="miniTitle">
                    Short description:
                </div>
            </div>
            <div class="textLine -tagline" v-tooltip.bottom-start="assetTagline">
                {{assetTagline}}
            </div>
        </div>

        <div class="detailBlock -tags">
            <div class="contactLine">
                <div class="miniTitle">
                    Tags:
                </div>
            </div>
            <div class="textLine -tags" v-tooltip.bottom-start="processedAssetTags">
                {{processedAssetTags}}
            </div>
        </div>

        <div class="assetActions">
            <slot v-if="hasGallery">
                <div class="button" @click="openGallery()" v-tooltip.bottom-start="`View showcase gallery`">
                    <div class="sprite general-logo"></div>
                </div>
                <LightBox
                        :showLightBox="false"
                        ref="lightboxGallery"
                        :images="assetGalleryLightBoxImageSettings"
                        :showThumbs="true"
                ></LightBox>
            </slot>

            <slot v-if="this.$props.status !== 'backup'">
                <div class="button" @click="backupCheckExist()" v-tooltip.bottom-start="`Backup this asset`">
                    <div class="sprite translate-original-grey"></div>
                </div>
            </slot>

            <slot v-if="this.$props.status === 'backup'">
                <div class="button" @click="restoreBackupAsset()"
                     v-tooltip.bottom-start="`Restore backup of this asset`">
                    <div class="sprite translate-new-grey"></div>
                </div>
            </slot>

            <slot v-if="this.$props.status !== 'backup'">
                <div class="button" @click="deleteCheckExist()" v-tooltip.bottom-start="`Delete this asset`">
                    <div class="sprite general-remove"></div>
                </div>
            </slot>

            <slot v-if="this.$props.status === 'backup'">
                <div class="button" @click="deleteAssetBackupConfirm()"
                     v-tooltip.bottom-start="`Delete backup of this asset`">
                    <div class="sprite general-remove"></div>
                </div>
            </slot>


        </div>


    </div>
</template>

<script>

   // NPM Packages
   const fs = require('fs')
   const getSize = require('get-folder-size')
   //const chokidar = require('chokidar')

   // VUE vendor components
   import LightBox from 'vue-image-lightbox'

   // Others
   import placeholderSymbol from '../../../../assets/images/icons/placeholderSymbol.png'

   export default {
      name: "assetMiniature",
      components: {
         LightBox
      },
      props:
          [
             'folder',
             'status',
             'postLoadAdded',
             'config'
          ]
      ,
      data: function () {
         return {

            // Globals
            userDataFolderPath: this.$store.getters.getUserDataFolder,
            assetsFolderPath: this.$store.getters.getAssetsFolder,
            assetsFolderPathMKBackup: this.$store.getters.getAssetsFolderBackup,
            assetSizeInterval: false,

            // Folder technical info
            assetSize: 'N/A',

            // Asset processing status
            isDisabled: false,

            // Asset info
            assetName: this.$props.folder,
            assetVersion: '',

            hasPreview: false,
            assetPreview: placeholderSymbol,
            assetPreviewTitle: 'No preview',
            assetPreviewLightBoxImageSettings: [],

            assetAuthorName: 'N/A',
            assetAuthorMail: false,
            assetAuthorUrl: false,
            assetAuthorDonationURL: false,

            assetDistributorName: 'N/A',
            assetDistributorMail: false,
            assetDistributorUrl: false,
            assetDistributorDonationURL: false,

            assetTagline: 'N/A',

            hasGallery: false,
            assetGalleryLightBoxImageSettings: [],

            assetLicense: 'N/A',
            assetLicenseURL: false,
            assetLicenseFile: false,

            hasCommentary: false,
            assetCommentaryFile: false,

            assetCommercialUse: 'N/A',
            assetCommercialURL: false,

            assetTags: ['N/A'],


         }
      }
      ,
      computed: {
         processedAssetTags: function () {
            return this.assetTags.join(', ')
         }
      },
      mounted: function () {

         this.getAssetSize()
         this.reloadAssetData()

         this.assetSizeInterval = setInterval(() => {
            this.getAssetSize()
         }, 10000)

      },

      beforeDestroy: function () {
         clearInterval(this.assetSizeInterval)
      },

      beforeUpdate: function () {
         this.getAssetSize()
         this.reloadAssetData()
      },

      methods: {
         // Utility methods
         openPreview() {
            this.$refs.lightboxPreview.showImage(0)
         },
         openGallery() {
            this.$refs.lightboxGallery.showImage(0)
         },
         openURL(link) {
            this.$electron.shell.openExternal(link)
         },

         // Folder detail methods
         getAssetSize() {

            let folderPath

            if (this.$props.status === 'normal') folderPath = this.assetsFolderPath + '/' + this.$props.folder
            if (this.$props.status === 'backup') folderPath = this.assetsFolderPathMKBackup + '/' + this.$props.folder


            //console.log('getting size')

            getSize(folderPath, (err, size) => {
               if (!err) {
                  this.assetSize = (size / 1024 / 1024).toFixed(1) + ' MB'
               }
            })

         },
         reloadAssetData() {

            let folderPath
            let configData = this.$props.config

            if (this.$props.status === 'normal') folderPath = this.assetsFolderPath + '/' + this.$props.folder
            if (this.$props.status === 'backup') folderPath = this.assetsFolderPathMKBackup + '/' + this.$props.folder


            // Reset all data to defaults first
            this.assetName = this.$props.folder
            this.assetVersion = ''

            this.hasPreview = false
            this.assetPreview = placeholderSymbol
            this.assetPreviewTitle = 'No preview'
            this.assetPreviewLightBoxImageSettings = []

            this.assetAuthorName = 'N/A'
            this.assetAuthorMail = false
            this.assetAuthorUrl = false
            this.assetAuthorDonationURL = false

            this.assetDistributorName = 'N/A'
            this.assetDistributorMail = false
            this.assetDistributorUrl = false
            this.assetDistributorDonationURL = false

            this.assetTagline = 'N/A'

            this.hasGallery = false
            this.assetGalleryLightBoxImageSettings = []

            this.assetLicense = 'N/A'
            this.assetLicenseURL = false
            this.assetLicenseFile = false

            this.hasCommentary = false
            this.assetCommentaryFile = false

            this.assetCommercialUse = 'N/A'
            this.assetCommercialURL = false

            this.assetTags = ['N/A']

            // Read file if we have config data
            if (configData) {
               const metaFilesPath = folderPath + '/metafiles/'

               if (configData.basicInformation !== undefined) {

                  // Get basic info
                  if (configData.basicInformation.name !== undefined) {
                     this.assetName = configData.basicInformation.name
                  }
                  if (configData.basicInformation.version !== undefined) {
                     this.assetVersion = configData.basicInformation.version
                  }

                  // Get preview image & set preview title
                  if (fs.existsSync(metaFilesPath + 'preview.jpg')) {
                     this.hasPreview = true
                     this.assetPreview = metaFilesPath + 'preview.jpg'
                     this.assetPreviewTitle = "Click for preview of: " + this.assetName
                     this.assetPreviewLightBoxImageSettings = [
                        {
                           thumb: metaFilesPath + 'preview.jpg',
                           src: metaFilesPath + 'preview.jpg',
                        }
                     ]
                  }
                  if (fs.existsSync(metaFilesPath + 'preview.png')) {
                     this.hasPreview = true
                     this.assetPreview = metaFilesPath + 'preview.png'
                     this.assetPreviewTitle = "Click for preview of: " + this.assetName
                     this.assetPreviewLightBoxImageSettings = [
                        {
                           src: metaFilesPath + 'preview.png',
                        }
                     ]
                  }

                  // Get author info
                  if (configData.basicInformation.author !== undefined && configData.basicInformation.author.exists) {

                     if (configData.basicInformation.author.name) {
                        this.assetAuthorName = configData.basicInformation.author.name
                     }

                     if (configData.basicInformation.author.mail) {
                        this.assetAuthorMail = configData.basicInformation.author.mail
                     }

                     if (configData.basicInformation.author.url) {
                        this.assetAuthorUrl = configData.basicInformation.author.url
                     }

                     if (configData.basicInformation.author.donationURL) {
                        this.assetAuthorDonationURL = configData.basicInformation.author.donationURL
                     }
                  }

                  // Get distributor info
                  if (configData.basicInformation.distributor !== undefined && configData.basicInformation.distributor.exists) {

                     if (configData.basicInformation.distributor.name) {
                        this.assetDistributorName = configData.basicInformation.distributor.name
                     }

                     if (configData.basicInformation.distributor.mail) {
                        this.assetDistributorMail = configData.basicInformation.distributor.mail
                     }

                     if (configData.basicInformation.distributor.url) {
                        this.assetDistributorUrl = configData.basicInformation.distributor.url
                     }

                     if (configData.basicInformation.distributor.donationURL) {
                        this.assetDistributorDonationURL = configData.basicInformation.distributor.donationURL
                     }
                  }

                  // Get tagline
                  if (configData.basicInformation.tagLine !== undefined) {
                     this.assetTagline = configData.basicInformation.tagLine
                  }

                  // Get gallery
                  if (fs.existsSync(metaFilesPath + 'gallery')) {


                     const galleryImages = fs.readdirSync(metaFilesPath + 'gallery', 'utf8', function (err, data) {
                        if (err) {
                           console.log(err)

                        } else {
                           //console.log(data)

                           return data

                        }
                     })


                     // Set gallery only if it has some actual images in it
                     if (galleryImages.length > 0) {
                        this.hasGallery = true

                        this.assetGalleryLightBoxImageSettings = []

                        this.assetGalleryLightBoxImageSettings = galleryImages.map(image => {

                           // Fix file pathing for background images
                           let filePath = 'file:///' + metaFilesPath + 'gallery/' + image
                           filePath = filePath.replace(/\\/g, "/")

                           return {
                              thumb: filePath,
                              src: metaFilesPath + 'gallery/' + image,
                           }
                        })
                     }
                  }

                  // Get license & commentary
                  if (configData.basicInformation.license !== undefined && configData.basicInformation.license.hasLicense) {

                     this.assetLicense = configData.basicInformation.license.type

                     // Get external link
                     if (configData.basicInformation.license.externalLink) {

                        switch (configData.basicInformation.license.type) {

                           case 'CC BY 4.0':
                              // code block
                              this.assetLicenseURL = 'https://creativecommons.org/licenses/by/4.0/'
                              break

                            // Try google if we dont get a match
                           default:
                              this.assetLicenseURL = 'https://www.google.com/search?q=license ' + configData.basicInformation.license.type
                        }

                     }

                     // Get local file
                     if (configData.basicInformation.license.localFile) {

                        if (fs.existsSync(metaFilesPath + 'license.pdf')) {
                           this.assetLicenseFile = metaFilesPath + 'license.pdf'
                        }
                        else {
                           // Try google if the file isnt there
                           this.assetLicenseURL = 'https://www.google.com/search?q=license ' + configData.basicInformation.license.type
                        }

                     }

                     // Get commentary file
                     if (configData.basicInformation.license.commentary) {

                        this.hasCommentary = false

                        // Try txt
                        if (fs.existsSync(metaFilesPath + 'commentary.txt')) {
                           this.assetCommentaryFile = metaFilesPath + 'commentary.txt'
                           this.hasCommentary = true
                        }

                        // Try PDF, preferred
                        if (fs.existsSync(metaFilesPath + 'commentary.pdf')) {
                           this.assetCommentaryFile = metaFilesPath + 'commentary.pdf'
                           this.hasCommentary = true
                        }


                     }

                  }

                  // Get commercial use info
                  if (configData.basicInformation.commercialUse !== undefined) {

                     if (configData.basicInformation.commercialUse) {
                        this.assetCommercialUse = 'Allowed'
                     } else {
                        this.assetCommercialUse = 'Not-allowed'
                     }

                     if (configData.basicInformation.commercialURL) {
                        this.assetCommercialURL = configData.basicInformation.commercialURL
                     }

                  }

                  // Get tags
                  if (configData.basicInformation.tags !== undefined) {
                     this.assetTags = configData.basicInformation.tags
                  }
               }
            }


         },

         // Backup asset
         backupCheckExist() {

            let folderBackupPath = this.assetsFolderPathMKBackup + '/' + this.$props.folder

            if (fs.existsSync(folderBackupPath)) {
               this.backupAssetConfirm()
            } else {
               this.backupAsset()
            }
         },
         backupAssetConfirm() {
            this.$dialog
                .confirm('Asset is already backed up. Overwrite with this version?')
                .then(() => {
                   this.backupAsset()
                })
                .catch(() => {
                })
         },
         backupAsset() {
            this.isDisabled = true
            setTimeout(() => {
               this.$store.dispatch('backupAsset', this.$props.folder).then(() => {
                  this.$awn.success("Asset backed up")
                  this.isDisabled = false
               })
            }, 250)
         },

         // Restore backed-up asset
         restoreBackupAsset() {
            this.isDisabled = true
            setTimeout(() => {
               this.$store.dispatch('restoreBackupAsset', this.$props.folder).then(() => {
                  this.$awn.success("Asset restored")
                  this.isDisabled = false
               })
            }, 250)
         },

         // Delete asset backup
         deleteAssetBackupConfirm() {
            this.$dialog
                .confirm('Delete backup of the asset? It can be retrieved manually later, but gets removed from the list')
                .then(() => {
                   this.deleteAssetBackup()
                })
                .catch(() => {

                })
         },
         deleteAssetBackup() {
            this.isDisabled = true
            setTimeout(() => {
               this.$store.dispatch('deleteAssetBackup', this.$props.folder)
                   .then(() => {

                      this.$awn.success("Asset backup deleted")
                      //this.isDisabled = false

                   })
            }, 250)
         },

         // Delete asset
         deleteCheckExist() {

            if (fs.existsSync(this.assetsFolderPathMKBackup + '/' + this.$props.folder)) {
               this.deleteAsset()
            } else {
               this.deleteAssetConfirm()
            }
         },
         deleteAssetConfirm() {
            this.$dialog
                .confirm('This asset is not backed up. Backup before deleting so it can be restored later?',
                    {
                       okText: 'Next step'
                    })
                .then(() => {
                   this.deleteAssetConfirm2()
                })
                .catch(() => {
                   //this.deleteAsset()
                })
         },
         deleteAssetConfirm2() {
            this.$dialog
                .confirm('Chose to delete with or without backup.',
                    {
                       okText: 'Delete & backup',
                       cancelText: 'Delete'
                    })
                .then(() => {
                   this.backupAsset()
                   this.deleteAsset()

                })
                .catch(() => {
                   this.deleteAsset()

                })
         },
         deleteAsset() {
            this.isDisabled = true
            setTimeout(() => {
               this.$store.dispatch('deleteAsset', this.$props.folder)
                   .then(() => {
                      this.$awn.success("Asset deleted")
                      //this.isDisabled = false
                   })
            }, 250)

         },


      }
   }
</script>


<style lang="sass" scoped>


    .assetMiniature
        font-family: "Montserrat", sans-serif
        width: 450px
        height: 405px
        background-color: rgba(43, 43, 43, 0.9)
        margin: 50px
        color: #fff
        border-radius: 3px
        letter-spacing: 1.5px
        padding: 15px
        position: relative
        display: flex
        flex-flow: column

        &:before,
        &:after
            content: ''
            pointer-events: none

        &.isDisabled
            &:before
                pointer-events: auto
                +M_AbsoluteFullCover
                z-index: 4000
                mix-blend-mode: hue
                background-color: black

            &:after
                pointer-events: auto
                +M_AbsoluteFullCover
                z-index: 40001
                mix-blend-mode: hue
                background-color: black

        &.hasSettings
            .titleInfo
                .title
                    color: #a8d790

        &.isBackup
            filter: grayscale(85%)
            background-color: rgba(0, 0, 0, 0.8)

        .masterOverlay
            +M_AbsoluteFullCover
            z-index: 5000
            display: flex
            justify-content: center
            align-items: center

            .overlayContentWrapper
                background-image: url('~@/assets/images/backgrounds/topBarBackground.jpg')
                background-size: cover
                padding: 50px
                border-radius: 5px
                display: flex
                flex-direction: column
                align-items: center
                border: 2px solid rgba(0, 0, 0, 0.5)

                .overlayMessage
                    font-family: "Elementary Gothic", sans-serif
                    font-size: 18px
                    font-weight: 600
                    color: #fff
                    letter-spacing: 3px
                    filter: drop-shadow(0px 0px 3px rgba(255, 255, 255, 0.7))

                .spinnerWrapper
                    margin-bottom: 20px
                    > div
                        filter: drop-shadow(0px 0px 3px rgba(255, 255, 255, 1))
                        color: #dcdcdc
                        width: 75px
                        height: 75px

        .icon
            cursor: pointer

            &.-important
                filter: invert(40%) grayscale(100%) brightness(60%) sepia(100%) hue-rotate(-50deg) saturate(400%) contrast(2)

            &.-positive
                filter: grayscale(100%) brightness(50%) sepia(100%) hue-rotate(50deg) saturate(600%) contrast(0.8)

        .titleInfo
            display: flex
            justify-content: space-between
            align-items: flex-end
            padding-bottom: 10px

            .title
                font-family: "Elementary Gothic", sans-serif
                font-size: 14px
                font-weight: 600
                white-space: nowrap
                overflow: hidden
                text-overflow: ellipsis
                flex-grow: 1
                flex-shrink: 0
                color: #d7896c
                max-width: 315px

            .dataWrapper
                flex-shrink: 0
                display: flex

                .version
                    font-family: "Elementary Gothic", sans-serif
                    color: #8f9298
                    font-size: 8.5px
                    flex-shrink: 0

        .content
            display: flex

            .preview
                width: 200px
                height: 200px
                background-image: url('~@/assets/images/backgrounds/assetMiniatureBackground.png')
                border-radius: 3px

                display: flex
                justify-content: center
                align-items: center
                flex-shrink: 0
                position: relative

                &:before
                    content: ''
                    +M_AbsoluteFullCover
                    background-color: rgba(0, 0, 0, .25)

                img
                    max-height: 80%
                    max-width: 80%

                &.hasPreview
                    cursor: pointer
                    transition: $transition-DefaultType 0.5s all

                    &:before
                        display: none

                    img
                        transition: $transition-DefaultType 0.5s all
                        cursor: pointer

                    &:hover
                        box-shadow: inset 0 0 50px 10px #fff
                        img
                            max-height: 100%
                            max-width: 100%

        .textDetails
            padding-top: 5px
            padding-left: 15px
            flex-grow: 1

            > div:last-child
                margin-bottom: 0

        .detailBlock
            margin-bottom: 15px

            &.-tagline
                margin-top: 15px

        .contactLine
            display: flex
            flex-grow: 1
            align-items: flex-end
            justify-content: space-between
            margin-bottom: 2px

            .miniTitle
                font-family: "Titania", sans-serif
                font-size: 13px
                color: #b8b8b8

            .contactIcons
                display: flex

                .icon
                    margin-left: 7px
                    transform: scale(0.85)

        .textLine
            font-size: 16px

            &.-positive
                color: #a8d790

            &.-negative
                color: #d7896c

            &.-tagline
                font-size: 13px
                display: -webkit-box
                -webkit-line-clamp: 2
                -webkit-box-orient: vertical
                overflow: hidden

            &.-tags
                font-size: 13px
                white-space: nowrap
                overflow: hidden
                text-overflow: ellipsis
                width: 100%

        .assetActions
            display: flex
            margin-top: auto

            .button
                flex-grow: 0

                // Display
                overflow: hidden
                display: flex
                justify-content: center
                align-items: center

                // Sizing & positioning
                position: relative
                height: 40px
                width: 40px
                margin-left: 10px

                // Background
                background-image: url('~@/assets/images/backgrounds/buttonBackground.png')
                background-size: 105px
                background-position-y: -6px

                // Border
                //border: 1px solid rgba(45, 21, 5, 0.7)
                border: 1px solid rgba(45, 21, 5, 0.7)

                // Other
                cursor: pointer
                border-radius: 3px

                filter: sepia(65%)

                &:first-child
                    margin-left: 0

                &:hover:not(.disabled),
                &.-active
                    // Text settings
                    color: #ffffff

                    // Other
                    filter: sepia(0%)

                &.disabled
                    cursor: no-drop
                    filter: grayscale(1) !important

                .sprite
                    // Sizing & positioning
                    filter: drop-shadow(0px 0px 5px rgba(255, 255, 255, 0.5))

                    pointer-events: none


</style>
