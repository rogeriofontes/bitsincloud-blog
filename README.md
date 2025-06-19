hugo new posts/meu-primeiro-post.md

git submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt
git submodule update --init --recursive

#[taxonomies]
#  category = "categories"
#  tag = "tags"


==============
hugo
cd public
git init
git remote add origin https://github.com/SEU-USUARIO/meu-blog.git
git checkout -b gh-pages
git add .
git commit -m "Publicação do blog com Hugo"
git push -f origin gh-pages
git remote add origin https://github.com/rogeriofontes/bitincloud.git

https://gohugo.io/getting-started/usage/
hugo --minify
hugo version

git worktree add -B gh-pages public origin/gh-pages
hugo
cd public
git add .
git commit -m "Deploy do site"
git push origin gh-pages