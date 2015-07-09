---
layout: default
modal-id: 1
date: 2015-06-12
img: blocipedia.png
alt: image-alt
project-date: June 2015
client: Start Bootstrap
category: Web Development
description: Use this area of the page to describe your project. The icon above is part of a free icon set by <a href="https://sellfy.com/p/8Q9P/jV3VZ/">Flat Icons</a>. On their website, you can download their free set with 16 icons, or you can purchase the entire set with 146 icons for only $12!

---

>As its name indicates, Blocipedia is a imitation of Wikipedia, where users can create their own wikis and share them publicly or privately.

#Explanation
The major difference between Blocipedia and Wikipedia is that we allow private wikis in Blocipedia. Users can upgrade to premium and thus be able to create private wikis which is open to those who is given privilege by the creator.

#Problem
How to connect the user role with the payment status was one of the major problems. The other is how to add and remove user who is accessible to a private wiki.

#Solution
The first problem can be solved by using `Stripe Gem`, and `ChargesController`, where a `create` action is used to link successful charge to upgrading user role. 

{% highlight ruby%}
def create
  customer = Stripe::Customer.create(email: current_user.email, card: params[:stripeToken])

    charge = Stripe::Charge.create( customer: customer.id, amount: 100,
        description: "BigMoney Membership - #{current_user.email}",
        currency: 'usd'
      )
    
    up_grade
    redirect_to current_user

    rescue Stripe::CardError => e 
      flash[:error] = e.message
      redirect_to new_charge_path
end
{% endhighlight %}

The `up_grade` is defined in a helper

{% highlight ruby%}
def up_grade
    if current_user.role == "standard"
      current_user.update_attribute(:role, "premium")
    end
    if current_user.save
      flash[:notice] = "You have successfully upgraded your account!"
    else
      flash[:error] = "Error"
    end
 end
{% endhighlight %}

The other problem, adding collaborator to a private wiki, is more complicated than it looks like. First, we have to keep a many-to-many relation to record the relation between private wikis and users. Second, we need to search a user by his email on `wiki#update` view without reloading the page. Third, we have to show wiki collaborators to the owner, along with links to remove each of them. 

Since rails and its database(SQLite and PostgreSQL) are not convenient with many-to-many relationship, we use `Redis` gem to retrieve the list of private wikis that certain user has access to, and the list of users that certain private wiki are open to.  We add `have_access_to` action to user’s model in `user.rb` and `give_access_to`, `open_to`, `delete_collaborator` action to wiki’s model in order to create, show, and remove collaborators

{% highlight ruby%}
def have_access_to
    wiki_ids = $redis.smembers(self.redis_key(:have_access_to))
    Wiki.where(:id => wiki_ids)
end

def give_access_to(user)
    $redis.multi do 
        $redis.sadd(user.redis_key(:have_access_to),self.id)
        $redis.sadd(self.redis_key(:open_to), user.id)
    end
end

def open_to
    user_ids = $redis.smembers(self.redis_key(:open_to))
    User.where(:id => user_ids)
end
def delete_collaborator(user)
    $redis.multi do
      $redis.srem(user.redis_key(:have_access_to), self.id)
      $redis.srem(self.redis_key(:open_to), user.id)
    end
end
{% endhighlight %}

To add the collaborators, we created `CollaboratorController` and corresponding views, js, and partials. We use jquery so that adding collaborator can be done without reloading the page. To show the collaborators, the partial `_collaborator.html.erb` is called by `wiki#show`. The javascript `create.js` and `destroy.js` is added so added or removed collaborator will show or hide automatically. More detail can be found in [GitHub](https://github.com/yuqiqian/Blocipedia)