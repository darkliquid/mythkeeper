<template>

    <div>
        <slot v-if="!this.$props.confirmMessage">
            <div
                    class="topCommandLineButton"
                    :class="[{ 'disabled': disabled},{'hasText': text}]"
                    :title="title"
                    v-on:click="nonConfirmAction">
                {{text}}
                <div class="icon sprite"
                     :class="[icon]">
                </div>

            </div>
        </slot>

        <slot v-if="this.$props.confirmMessage">
            <div
                    class="topCommandLineButton"
                    :class="[{ 'disabled': disabled},{'hasText': text}]"
                    :title="title"
                    v-on:click="confirmAction">
                {{text}}
                <div class="icon sprite"
                     :class="[icon]">
                </div>

            </div>
        </slot>

    </div>


</template>

<script>
   export default {
      name: "topCommandLineButton",
      computed: {},
      data: function () {
         return {}
      },
      methods: {

         nonConfirmAction(event) {
            if (!event.target.classList.contains('disabled')) {
               this.$props.action()
            }
         },
         confirmAction(event) {

            // Dont trigger if disabled
            if (!event.target.classList.contains('disabled')) {
               this.$dialog
                   .confirm(this.$props.confirmMessage)
                   .then(() => {

                      this.$props.action()
                   })
                   .catch(() => {
                   })
            }

         }
      },
      props: {
         title: String,
         text: String,
         action: false,
         confirmMessage: false,
         disabled: {
            type: Boolean,
            default: false,
         },
         icon: {
            type: String,
            default: 'alert-error',
         },

      },

   }
</script>

<style lang="sass">
    .topCommandLineButton

        // Display
        overflow: hidden
        display: flex
        justify-content: center
        align-items: center

        // Sizing & positioning
        position: relative
        height: 40px
        width: 40px
        margin-left: 15px

        // Background
        background-image: url('~@/assets/images/backgrounds/buttonBackground.png')
        background-size: 105px
        background-position-y: -6px
        color: #ffffff

        // Border
        //border: 1px solid rgba(45, 21, 5, 0.7)
        border: 1px solid rgba(45, 21, 5, 0.7)

        // Other
        cursor: pointer
        border-radius: 3px

        filter: sepia(65%)


        &.hasText
            width: inherit
            padding: 0 10px

            .sprite
                margin-left: 10px

        &:hover:not(.disabled),
        &.-active
            // Text settings
            color: #ffffff

            // Other
            filter: sepia(0%)

        &.disabled
            cursor: no-drop
            filter: grayscale(1) !important

        .icon
            // Sizing & positioning
            filter: drop-shadow(0px 0px 5px rgba(255, 255, 255, 0.5))

            pointer-events: none
            transition: $transition-DefaultType 0.5s all


</style>

