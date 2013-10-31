# Auth0 Login Widget 2 (beta)

The __Auth0 Login Widget__ makes it easy to integrate SSO in your app. You won't have to worry about:

* Having a professional looking login dialog that displays well on any resolution and device.
* Finding the right icons for popular social providers.
* Remembering what was the identity provider the user chose the last time.
* Solving the home realm discovery challenge with enterprise users (i.e.: asking the enterprise user the email, and redirecting to the right enterprise identity provider).
* Implementing a standard sign in protocol (OpenID Connect / OAuth2 Login)

## Playground

@@sdk2@@

## Anatomy of the Auth0 Login Widget

![](img/widget-numbered.png)

1. The __title__ of the widget. You can optionally show a 32x32 icon.
2. The __social buttons__ will be shown if you have at least one social connection enabled.
3. The __Email__ field will be shown if you have at least one enterprise connection enabled. The __Password__ field will be shown if you have a Database connection. 
4. The __Sign Up__ and __Forgot Password__ links will be shown if you have a Database connection. 

> **How does enterprise SSO work?** Consider a user that enters john@**fabrikam.com**. If there's an enterprise connection with an associated domain "**fabrikam.com**", then the password field will be hidden. When the user clicks on __Sign In__, he/she will be redirected to the corresponding identity provider (Google Apps, AD, Windows Azure AD, etc.) where that domain is registered. If the user is already logged in with the Identity Provider, then Single Sign On will happen.

## Including the Login Widget on your page
Add the script tag to your page to get started with __Auth0 Login Widget__.

    <script src="@@sdk2URL@@/auth0-widget.min.js"></script>

## Invoking the Widget

To invoke the widget, create an instance of `Auth0Widget`:

    var widget = new Auth0Widget({
        domain:       '@@account.namespace@@',
        clientID:     '@@account.clientId@@', 
        callbackURL:  '@@account.callback@@'
    });

and then, use the `show` method:

    widget.show();
    // or
    widget.show(options, callback);

## Customizing the Widget

The Widget can be customized through the `options` parameter sent to the `show` method.

### Options

* __connections__: Array of enabled connections that will be used for the widget. _Default: all enabled connections_.
* __container__: The id of the DIV where the widget will be contained.
* __icon__: Icon url. _Recommended: 32x32_.
* __showIcon__: Show/Hide widget icon. _Default: false_.
* __extraParameters__: JSON object to send extra parameters when starting a login.
* __resources__: JSON object that contains your customized text labels.

This example shows how to work with only specified connections and display the labels in Spanish:

    widget.show({
        connections: ['facebook', 'google-oauth2', 'twitter', 'Username-Password-Authentication'],
        resources: {
            title: "Contoso",
            loadingTitle: "Espere por favor...",
            signInButtonText: "Ingresar", 
            emailPlaceholder: "Correo", 
            passwordPlaceholder: "Contraseña",
            separatorText: "o",
            showIcon: true,
            icon: "https://myapp.com/logo.png",
            signupText: "Registrarme!", 
            forgotText: "Olvidé mi contraseña"
        },
        icon: 'https://contoso.com/logo-32.png',
        showIcon: true
    },
    funcion () {
      // The Auth0 Widget is now loaded.
    });

Resulting in:

![](img/widget-customized.png)

Is there an option that you think would be useful? Just <a target="_blank" href="https://github.com/auth0/auth0-widget.js/issues">open an issue on GitHub</a> and we'll look into adding it.

## Customizing the Login Widget for Database Connections

When using a database connections, the **Login Widget** has support for account creation (sign-up) and password changes:

### Customizing **sign-up** messages

    widget.show({
        // ... other options ... 
        resources: {
            // ... other resources ... 
            signupTitle: "Registrarse",
            signupHeaderText: "Por favor, ingrese su correo y contraseña",
            signupFooterText: "Al hacer clic en \"Crear Usuario\", usted está aceptando nuestros términos y condiciones.",
            signupButtonText: "Crear Usuario",
            signupEmailPlaceholder: "Correo",
            signupPasswordPlaceholder: "Contraseña",
            signupCancelButtonText: "Cancelar"
        }
    });

Will display the following:

![](img/widget-customized-signup.png)

### Customizing **change-password** messages

    widget.show({
        // ... other options ... 
        resources: {
            // ... other resources ... 
            resetTitle: "Cambiar Contraseña",
            resetHeaderText: "Por favor, ingrese su correo y una nueva contraseña. Se le enviará un correo para confirmar el cambio de la misma.",
            resetButtonText: "Enviar",
            resetEmailPlaceholder: "Correo",
            resetPasswordPlaceholder: "Nueva Contraseña",
            resetRepeatPasswordPlaceholder: "Confirmar Nueva Contraseña",
            resetCancelButtonText: "Cancelar",
            resetSuccessText: "Se ha enviado un correo electrónico para confirmar el cambio de contraseña.",
            resetEnterSamePasswordText: "Por favor, ingrese la misma contraseña"
        }
    });

Will display the following:

![](img/widget-customized-reset.png)

## Customizing error messages

You can also customize the error messages that will be displayed on certain situations:

    widget.show({
        // ... other options ... 
        resources: {
            // ... other resources ... 
            // wrongEmailPasswordErrorText, serverErrorText, signupEnterpriseEmailWarningText, signupServerErrorText and resetServerErrorText are used only if you have a Database connection
            wrongEmailPasswordErrorText: 'Custom error message for invalid user/pass.',
            serverErrorText: 'There was an error processing the sign in.',
            signupEnterpriseEmailWarningText: 'This domain {domain} has been configured for Single Sign On and you can\'t create an account. Try signing in instead.',
            signupServerErrorText: 'There was an unhandled error processing the sign up.',
            resetServerErrorText: 'There was an unhandled error processing the change password.',
            // strategyDomainInvalid is shown if the email does not have a matching enterprise connection
            strategyDomainInvalid: 'The domain {domain} has not been setup.',
            // strategyEmailInvalid is shown if the email is not valid
            strategyEmailInvalid: 'The email is invalid.'
        }
    });

These errors will be shown on the widget header:

![](img/widget-error.png)

## Sending extra parameters

You can send extra parameters when starting a login by adding attributes in the `extraParameters` option. The example below adds a `state` parameter with a value equal to `foo`.

    widget.show({
        // ... other options ... 
        extraParameters: {
            state: "foo"
        }
    });

## Using the API instead of the widget

If you don't want to use the widget, you can still make use of the API with a simple JavaScript call. Here is an example:

    var client = widget.getClient();

    // trigger login with google
    $('.login-google').click(function () {
        client.login({
            connection: 'google-oauth2'
        });
    });

    // trigger login with an enterprise connection
    $('.login-contoso').click(function () {
        client.login({
            connection: 'contoso.com'
        });
    });

    // trigger login with a db connection
    $('.login-dbconn').click(function () {
        client.login({
            connection: 'Username-Password-Authentication',
            username: 'jdoe',
            password: 'shhhhh'
        });
    });

For more information, please visit <a target="_blank" href="https://github.com/auth0/auth0.js">our project on GitHub</a>.

## FAQs

* Is it possible to embed the widget in an HTML element instead of using a modal dialog?

    Yes. You can embed it on a `div` specifying the div `id` from `options.container`.

* What happens if the user enters an email corresponding to a domain that has not been provisioned before? 

    An error message will be displayed (The domain has not been setup yet).