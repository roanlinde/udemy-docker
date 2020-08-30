### **Using Bind Mounts**

Bind mounts allow web servers to automatically detect changes made to source code and apply them immediately. For this example, we will run a Jekyll webserver as a container ontop of a code base - if anything in the code base is changed, it should reflect immediately on the webpage

```bash
git clone https://github.com/BretFisher/udemy-docker-mastery
cd udemy-docker-mastery/bindmount-sample-1/
docker run -p 80:4000 -v $(pwd):/site bretfisher/jekyll-serve
```