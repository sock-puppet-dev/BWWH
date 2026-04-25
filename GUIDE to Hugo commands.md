hugo server
hugo

rm -r public && hugo
hugo --cleanDestinationDir
hugo --cleanDestinationDir --minify

hugo new theme basic