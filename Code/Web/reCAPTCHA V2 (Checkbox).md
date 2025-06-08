#code #web #security #guide 

1. Go to https://www.google.com/recaptcha/admin/create and register a site.
2. Copy the site key
3. Add the following to `head`:
	```html
	<script src="https://www.google.com/recaptcha/api.js" async defer></script>
	<script>
		function onSubmit(token) {
			// Code to run when the captcha is solved
		}
	</script>
	```
4. Add the following code to display the captcha:
	```html
	<div class="g-recaptcha" data-sitekey="site-key"></div>
	```
	Replace `site-key` with the actual site key copied from step 1.