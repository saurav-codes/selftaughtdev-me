# Make your API to return only Required Fields

Hey Folks,

In this Blog, I am going to show you how can return only those fields from the API which user have requested by passing them in URL as parameters. this way your API users can only request those fields which they need & this will remove some unnecessary clutter from your API response.

if you don't understand about URL parameters then head toward [this link for some reference.](https://stackoverflow.com/questions/5095887/how-do-i-pass-a-url-with-multiple-parameters-into-a-url)

if you are a visual learner then checkout the video version of this blog. 

%%[yt-req-fields-api]


For Reference, I will be using a demo API which I created in the previous Blog. you can find the code for that API [here](https://selftaughtdev.me/build-blazing-fast-rest-api-using-django-elasticsearch-haystack).


![LetTheGamesBeginPanicGIF.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1665820308211/HjDy5XIC8.gif align="left")

Now, first pass some params in the URL Bar of your browser & then hit enter.


![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1665818029185/Y5qnnXExm.png align="left")


As you can see, even after passing some fields in URL Bar, the API is still returning all fields. Which is obvious as we haven't implemented any logic for that yet.

Now, let's implement that logic. For that, we will be using our `MovieHaystackSerializer` from previous Blog Code.
Now you will see some different code than what you usually write in your Serializer. Just Ignore new things as of now & only focus on `__init__` and `fields` of our Serializer. So, first, we will edit our `MovieHayStackSerializer` by overiding the `__init__` method.

```python
# 3rd party imports
from drf_haystack.serializers import HaystackSerializer

# local imports
from .search_indexes import MovieIndex


class MovieHayStackSerializer(HaystackSerializer):
    class Meta:
        # The `index_classes` attribute is a list of which search indexes
        # we want to include in the search.
        index_classes = [MovieIndex]

        # The `fields` contains all the fields we want to include.
        # NOTE: Make sure you don't confuse these with model attributes. These
        # fields belong to the search index!
        fields = [
            "title",
            "description",
            "year",
            "rating",
            "global_ranking",
            "length",
            "revenue",
            "genre",
            "country",
            "director",
        ]

    def __init__(self, *args, **kwargs):
        super(MovieHayStackSerializer, self).__init__(*args, **kwargs)
        # get the query params
        fields_in_url_params = self.context["request"].GET.get("fields", None)
        if fields_in_url_params:
            # split the params from comma to make a list
            fields_in_url_parmas_list = fields_in_url_params.split(",")
            existing_fields = set(self.fields)  # -> keys of all fields

            # get all the fields which are not in params but declared in meta class above
            # our_fields = {'title', 'description', 'year', '....'}
            # fields_from_urls = {'title', 'year'}
            # operation = our_fields - fields_from_url => {'description', '.....'}
            fields_to_remove = existing_fields - set(fields_in_url_parmas_list)

            # now since we have fields 
            for field in fields_to_remove:
                self.fields.pop(field)  # remove this field
```

Now, let's see what we have done here. First, we have overrided the `__init__` method of our Serializer. In this method, we are getting the query params from the URL & then we are splitting them by comma to make a list. After that, we are getting all the fields which are declared in our `Meta` class. Now, we are getting the difference between these two lists. The difference will be the fields which we have to remove from our Serializer. After that, we are removing those fields from our Serializer.


Now, let's see what happens when we pass some fields in URL Bar.


![CrossFingersPrayingGIF.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1665819195494/wVmX6DWKO.gif align="left")

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1665818780150/Xk2GHKIA1.png align="left")



![TheKingOfRandomSuccessGIF.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1665819894987/eNYQRVFMX.gif align="left")

As you can see, now we are only getting those fields which we have passed in URL Bar. This is how you can return only those fields from the API which user have requested by passing them in URL as parameters.
Link for Repo - https://github.com/selftaughtdev-me/movie-search-api


![ThatsAllLukeTillersonGIF.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1665819528638/XqoQzo6Bl.gif align="left")

If you have any questions or suggestions, feel free to comment below. I will be happy to help you. Also, if you like this Blog, then please share it with your friends & colleagues. you can also follow me on [Twitter](https://twitter.com/selftaughtdev_) & [LinkedIn](https://www.linkedin.com/in/selftaughtdev-me/) for getting updates about my new Blogs.




