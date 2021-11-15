<script context="module">
  export function load({ page }) {
    return {
      status: 302,
      redirect: '/' + page.params.user
    }
  }
</script>