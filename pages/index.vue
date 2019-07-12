<template>
  <div class="container">
    <div>
      <logo />
      <h1 class="title">
        sentry-error-test
      </h1>
      <h2 class="subtitle">
        just reload the page to throw some client and backend errors :)
      </h2>
    </div>
  </div>
</template>

<script lang="ts">
import Logo from '~/components/Logo.vue'
import { Component, Vue, Prop } from 'vue-property-decorator'

@Component({
components: { Logo }
})
export default class IndexPage extends Vue {
	mounted() {
		// this part is only called on client
    console.log('MOUNTED ON FRONTEND');
    this.$sentry.captureMessage('mounted'); // valid call for types/sentry.d.s which declares browser interface
	}

	created() {
		// this part can be called on server or on client
		if (process.server) {
		  console.log('HELLO FROM BACKEND');
      this.$sentry.captureMessage('backend'); // now we have the interface for the frontend/browser, but we would need the one for the backend
    } else {
		  console.log('HELLO FROM FRONTEND'); // valid call
    }

    /**
     * goal is that we just a single line of calling the captureMessage, error,..etc.
     * e.g.
     */
    this.someGlobalErrorHandlerImplementedAsClassMethod('hello from frontend and backend');
	}

	someGlobalErrorHandlerImplementedAsClassMethod(msg: string, level?: string, whatever?: any) {
	  if (process.server) {
	    this.$sentry.captureMessage(msg) // <-- use valid server signature call
    } else {
      this.$sentry.captureMessage(msg) // <-- use valid browser signature call
    }
  }
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
