cd /home/kali/Downloads/website/_sitecd /home/kali/Downloads/website/_site

git init

git remote add origin https://github.com/beastinvains/beastinvains.github.io

git checkout -b gh-pages

git add .

git commit -m "Deploy _site folder to gh-pages"

git push -u origin gh-pages --force


IMPORTENT NOTE 
-you should only use linex to build and deploy site
-if you are not direct admin then you have to add user and give permitions
-bundle exec jekyll serve 
