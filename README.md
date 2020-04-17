# gitlab-ci-yaml-validator

One might want to validate changes made to a .gitlab-ci.yml file before commiting changes. This could be to avoid seeing the pipeline error or just to have
a sanity check while making changes. 

Luckily gitlab offers an api-endpoint to enable this, found [here] (https://docs.gitlab.com/ee/api/lint.html). We can create a curl request to this 
api endpoint to check the file. Since a lot of us work on the terminal, we can create a function automate this. 

# Setup

I've used/tested it using iterm2 with oh-my-zsh. Python and the js-yaml-cli npm package is needed.

# Instructions

1. Install the npm package with ```npm install -g js-yaml-cli```

2. Open your ```.zshrc``` file with your text editor, most probably in ~/.zshrc 

3. Append the following function to the file

```
function gitlab-yaml-lint() {
    curl --header "Content-Type: application/json" https://gitlab.com/api/v4/ci/lint --data "{\"content\": $(cat $1 | yaml2json | tr -d '\n' | python -c 'import json,sys; print(json.dumps(sys.stdin.read()))')}"
}
```

4. Restart the shell with ```source ~/.zshrc```

5. Test it with ```gitlab-yaml-lint .gitlab-ci.yml``` while in the same folder as the .gitlab-ci.yml file

# FAQ

1. Does this work with custom Gitlab installations? Yes, just change the curl URL to your custom installation URL

2. How do I test .gitlab-ci.yml files that depend on private repositories (if include commands are used,etc)? You can create and add an API token to the 
curl request with ```--header "PRIVATE-TOKEN: ***"```

3. Can I avoid using python and yaml2json? Yes, the gitlab api just expects json (its a bit picky with formatting). As long as you can provide it the 
json content in the right format, it will work. One could potentially use tools such as ```jq``` or ```yq``` to achieve this


