---
layout: post
title:  "OAuth in React/Redux"
date:   2017-07-19 20:59:54 +0000
---


As I approached the end of the curriculum, I realized I still didn't have a good idea for a final project, so I decided to revisit the Dive Log app that I created using Sinatra, which you can read about here: [Diving into ActiveRecord](http://michaelries.info/2017/04/27/diving_into_activerecord/).  As a reminder, the app is a tool to store and retrieve critical information about your dives so that you can be prepared for future dives. The idea has a lot of potential to grow into a real product, so it made sense to redo it.

This time, the assignment required me to create a single-page app using React and Redux.  So I wrote a backend API using Rails to provide the data storage and retrieval, and then proceeded with the front end.   Creating the API was fairly straightforward and won't be discussed in detail here.

The nature of the app -- storing and retrieving dive data -- requires some sort of authentication so that you can trust that your data haven't been tampered with.  In addition, I detest being required to create new accounts for every web app that I visit, so I wanted to use [OAuth2](https://www.digitalocean.com/community/tutorials/an-introduction-to-oauth-2).  This seems like a common scenario, so I thought for sure there would be some third-party library that would make it easy.

I searched and found a couple that were promising: [redux-auth](https://github.com/lynndylanhurley/redux-auth#extended-documentation) and [react-native-oauth](https://github.com/fullstackreact/react-native-oauth), but they seemed unneccessarily complicated.  I suppose part of this was that I didn't understand JWT as well as I should.

After reading many blogs and tutorials I finally decided that it isn't really all that hard to do myself.  The flow looks something like the following diagram, using Facebook as an OAuth provider for example:

![OAuth flow](https://github.com/mikeries/dive-log-client/blob/master/public/Capture.PNG?raw=true)

Ok, so right now you're probably thinking that really doesn't look simple.  But most of the complexity is contained in the boxes with the red text, which handled without much fuss by the [Omniauth](https://github.com/omniauth/omniauth) gem or by [devise](https://github.com/plataformatec/devise).  Admittedly, devise isn't exactly a walk in the park to use, but it's popular enough you may have already run into it.  Personally, devise is overkill for this project, as I only wanted OAuth and had no need for password resets, 2-factor authentication, etc, so I went with the omniauth gem.

After the API authenticates the user, it creates a JWT and sends it to the user.  A JWT is a [JSON Web Token](https://jwt.io/introduction/) which is an industry standard method for passing data securely in a digitally signed way.  To create this JWT, the API combines the data, the header, and a secret key and mixes it all up into a secure package that can be transmitted to the client.

There are lots of resources out there on how to create and use JWTs: 

- [5 Easy Steps to Understanding JSON Web Tokens (JWT)](https://medium.com/vandium-software/5-easy-steps-to-understanding-json-web-tokens-jwt-1164c0adfcec)
- [Secure Your React and Redux App with JWT Authentication](https://auth0.com/blog/secure-your-react-and-redux-app-with-jwt-authentication/)
- [Authentication with Rails, JWT and ReactJS](https://nebulab.it/blog/authentication-with-rails-jwt-and-react/)
- [Build an app with Rails and React - User authentication](https://groundberry.github.io/development/2017/04/08/build-an-app-with-rails-and-react-user-authentication.html)
- [React and Redux Sagas Authentication App Tutorial](http://start.jcolemorrison.com/react-and-redux-sagas-authentication-app-tutorial/)

My favorite was actually written by a Flatiron Student/Instructor:

- [JWT Authentication with React + Redux](http://www.thegreatcodeadventure.com/jwt-authentication-with-react-redux/)

On the server side, I used the [jwt gem](https://github.com/jwt/ruby-jwt) to encode and decode the tokens.  I wrote a helper class to do the dirty work:

```ruby
class Auth
  def self.encode_uid(user_id)
    payload = {user_id: user_id}
    JWT.encode payload, ENV['AUTH_SECRET'], 'HS256'
  end

  def self.decode_uid(token)
    payload = JWT.decode token, ENV['AUTH_SECRET'], true, { :algorithm => 'HS256' }
    payload[0]['user_id']
  end
end
```

Note that we store the secret in environment variables so we don't accidentally upload it to github!

In the application controller, we have some helper methods that perform the authentication and extract the current_user:

```ruby
class ApplicationController < ActionController::API  
  before_action :authenticate 

  def logged_in?
    !!current_user
  end

  def current_user
    return @current_user if @current_user
    if auth_present?
      uid = Auth.decode_uid(read_token_from_request)
      @current_user = User.find_by({uid: uid})
      return @current_user if @current_user
    end
  end

  def authenticate
    render json: {error: "unauthorized"}, status: 401 unless logged_in?
  end

  private

  def read_token_from_request
    token = request.env["HTTP_AUTHORIZATION"].scan(/Bearer: (.*)$/).flatten.last
  end

  def auth_present?
    !!request.env.fetch("HTTP_AUTHORIZATION", "").scan(/Bearer/).flatten.first
  end
end
```

The nice thing about this implementation is that there isn't any need to encode/decode JWT on the client.  All the client needs to do is store the token (I stored it in [sessionStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage)) and put it in the header for each request it makes to the api.  For example, a get method looks like this:

```javascript
  get(url, jwt) {
    const headers =  {
      'Accept': 'application/json',
      'Content-Type': 'application/json',
      'Authorization': `Bearer: ${jwt}`
    }
    return fetch(`${API_URL}${url}`, {
      method: 'GET',
      headers: headers
    }).then(response => (response.json()));
  },
```

Now for the disclaimers.  This method does cause a page refresh when you first log in, because the api redirects to facebook, and then back to the client.  I'm not sure it's possible to avoid this unless the OAuth provider supports authentication without a redirect.

Also, I've left out some of details for the error handling.  That's next on my to-do list.

As always, please feel free to contact me with any questions, suggestions, corrections, or just to say hi.

Slack: @michaelries
email: michael.ries@gmail
	
[API Github Repository](http://https://github.com/mikeries/dive-log-api)
[Client Github Repository](https://github.com/mikeries/dive-log-client)



