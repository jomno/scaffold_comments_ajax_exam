scaffold + comments ajax 예제
=
rails 
## 준비 작업
* bash
```bash
rails g scaffold post title content:text
rails g model comment post:references body
rake db:migrate
```
* config/routes.rb
```ruby
[...]
  root "posts#index"
  resources :posts
  post "posts/:id/comments" => "posts#comments"
[...]
```
* app/views/posts/show.html.erb
```html
<form action="/posts/<%= @post.id %>/comments" method="post" data-remote="true">
    <label>댓글 작성</label>
    <input type="text" name="body" id="comment_body">
    <input type="submit">
</form>
<div id="body">
    <% @comments.each do |comment| %>
        <hr>
        <%= comment.body%>
    <% end %>
</div>
```
form 부분에 date-remote="true" 메소드를 추가해줘서 ajax 요청 생성
## 응답 코드
* app/controllers/posts_controller.rb
```ruby
[...]
  def show
    @comments = @post.comments
  end
  
  def comments
    @comment = Comment.new(body: params[:body])
    if @comment.save
      respond_to do |format|
        format.js #응답을 js로 하겠다.
      end
    end
  end
[...]
```
comments에서 Comment를 저장하면 respond_to 메소드를 사용해서 응답을 js로 함(format.js)
@comment변수도 넘어감
* app/views/home/comments.js.erb
```js
$('#comment_body').val("");
$('#body').append("<hr><%= @comment.body%>");
```
성공적으로 응답을 받으면 comments.js.erb로 응답이 가는데 이 후 input의 내용을 지우고<br>
body에다가 아까 저장이 성공한 @comment.body를 추가시킵니다.

완성!!

### Reference post
[rails ajax comment blog post](http://blog.saltfactory.net/using-ajax-and-partial-in-rails/)<br>
[괜찮은 블로그](http://wantknow.tistory.com/71)