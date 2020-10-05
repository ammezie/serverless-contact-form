<template>
  <div class="container">
    <CBox
      v-bind="mainStyles[colorMode]"
      d="flex"
      w="100vw"
      h="100vh"
      flex-dir="column"
      justify-content="center"
    >
      <CHeading text-align="center" mb="4">
        Serverless contact form
      </CHeading>
      <CFlex justify="center" direction="column" align="center">
        <CBox mb="3">
          <CIconButton
            mr="3"
            :icon="colorMode === 'light' ? 'moon' : 'sun'"
            :aria-label="`Switch to ${
              colorMode === 'light' ? 'dark' : 'light'
            } mode`"
            @click="toggleColorMode"
          />
        </CBox>

        <CBox text-align="left" width="50%">
          <form @submit.prevent="sendContactToLambdaFunction">
            <CFormControl>
              <CFormLabel for="name">
                Name
              </CFormLabel>
              <CInput id="name" v-model="form.name" type="text" aria-describedby="name" />
            </CFormControl>

            <CFormControl>
              <CFormLabel for="email">
                Email
              </CFormLabel>
              <CInput id="email" v-model="form.email" type="email" aria-describedby="email-helper-text" />
              <CFormHelperText id="email-helper-text">
                We'll never share your email.
              </CFormHelperText>
            </CFormControl>

            <CFormControl>
              <CFormLabel for="message">
                Message
              </CFormLabel>
              <CTextarea v-model="form.message" placeholder="Type your message" />
            </CFormControl>

            <CBox mt="12" d="flex" flex-dir="column" align="center">
              <CButton type="submit" right-icon="arrow-forward" width="20%" variant-color="vue" variant="outline">
                Submit
              </CButton>
            </CBox>
          </form>
        </CBox>
      </CFlex>
    </CBox>
  </div>
</template>

<script lang="js">
import {
  CBox,
  CButton,
  CIconButton,
  CFlex,
  CHeading,
  CTextarea,
  CFormControl,
  CFormLabel,
  CInput,
  CFormHelperText
} from '@chakra-ui/vue'

export default {
  name: 'App',
  inject: ['$chakraColorMode', '$toggleColorMode'],
  components: {
    CBox,
    CButton,
    CIconButton,
    CFlex,
    CHeading,
    CTextarea,
    CFormControl,
    CFormLabel,
    CInput,
    CFormHelperText
  },
  data () {
    return {
      mainStyles: {
        dark: {
          bg: 'gray.700',
          color: 'whiteAlpha.900'
        },
        light: {
          bg: 'white',
          color: 'gray.900'
        }
      },
      form: {
        name: '',
        email: '',
        message: ''
      }
    }
  },
  computed: {
    colorMode () {
      return this.$chakraColorMode()
    },
    theme () {
      return this.$chakraTheme()
    },
    toggleColorMode () {
      return this.$toggleColorMode
    }
  },
  methods: {
    async sendContactToLambdaFunction () {
      try {
        const response = await this.$axios.$post('/.netlify/functions/contact-mail', {
          issuerName: this.form.name,
          issuerEmail: this.form.email,
          message: this.form.message
        })

        this.$toast({
          title: 'Mail sent',
          description: response,
          status: 'success',
          duration: 10000,
          isClosable: true
        })

        this.form.name = ''
        this.form.email = ''
        this.form.message = ''
      } catch (error) {
        this.$toast({
          title: 'An error occured',
          description: error,
          status: 'error',
          duration: 10000,
          isClosable: true
        })
      }
    }
  }
}
</script>
