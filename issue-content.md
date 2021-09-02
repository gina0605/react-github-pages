내가 만든 index.html과 react app을 모두가 볼 수 있도록 배포하는 방법에 대해 정리해보았습니다.

**index.html 배포는 정말 쉬우니 한번 해보세요~~** :+1:

<br/>

# Github Pages란?
[공식 문서](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages)에는 다음과 같이 적혀있습니다.
> GitHub Pages is a static site hosting service that takes HTML, CSS, and JavaScript files straight from a repository on GitHub, optionally runs the files through a build process, and publishes a website.

즉, 우리가 만든 index.html, react app 등을 배포할 수 있게 해주는 서비스입니다.
예를 들어, 저는 과제0의 결과물을 https://gina0605.github.io/waffle-rookies-19.5-react-assignment-0/ 에 배포해두었습니다. 

이제 Github Pages를 설정하는 방법에 대해 알아보겠습니다.

<br/>

# index.html 배포하기
과제0에서 만든 index.html을 **아주아주 쉽게** 배포해봅시다.

1. assignment 브랜치에서 작업한 내용을 push한다.
1. 깃헙에서 자신의 repository로 간 후, Settings > Pages를 누른다.
1. Source에서 Branch로 assignment를 선택하고, Save를 누른다. 

사진

조금 기다리면 초록색으로 `https://username.github.io/waffle-rookies-19.5-react-assignment-0/`에 배포가 완료되었다고 뜰 것입니다.
이 링크를 들어가시면 성공적으로 배포 된 페이지를 볼 수 있습니다. 

Settings에서 github pages를 assignment 브랜치와 연결시켜놓았기 때문에,
앞으로 assignment 브랜치에 commit을 한 후 push를 할 때마다 해당 내용이 반영된 채 새로 배포가 될 것입니다.
commit history에 보이는 초록색 체크 표시는 배포가 성공적으로 되었다는 뜻입니다.

사진

<br/>

# React app 배포하기
이제 과제2였던 React app도 배포를 해봅시다.

## 방법
1. `create-react-app` 명령어로 만들어진 폴더를 깃헙에 등록해줍니다.
깃헙에서 새 레포지토리를 만들고, `git remote add`와 같은 명령어를 이용하시면 될 겁니다.
1. `npm install gh-pages --save`를 실행시킵니다.
[gh-pages](https://github.com/gitname/react-gh-pages)는 React app을 github pages에 배포하는 것을 도와주는 라이브러리입니다.
1. package.json 파일을 수정합니다.
    - `name` 등과 같은 레벨에 `"homepage": "http://username.github.io/{repositoryname}"`를 추가합니다.
    `{repositoryname}`에는 본인의 깃헙 레포 이름을 적으세요.
    - `scripts`에 아래 두 속성을 추가합니다.
        ```yaml
        "scripts": {
            //...
            "predeploy": "npm run build",
            "deploy": "gh-pages -d build"
        }
        ```
 1. `npm run deploy`를 실행시킵니다.
 
깃헙으로 가보면 gh-pages라는 브랜치가 만들어져있고, github pages 설정도 되어있을 것입니다.
조금 기다리면 https://username.github.io/repositoryname/ 에 배포가 될 것입니다.
Settings > Pages에서 사이트 링크를 확인할 수 있습니다.

이제부터 무언가를 수정한 후 다시 배포가 하고 싶을 때는, 로컬에서 `npm run deploy`를 실행하면 됩니다.

<br/>

## Github Actions 추가하기
이제 매번 `npm run deploy`를 실행하지 않아도, master branch를 기반으로 자동으로 배포가 되도록 해봅시다.
이를 위해서는 Github actions를 활용할 것입니다. 
 
[Github Actions](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions)는 push 등의 이벤트가 발생했을 때 배포 등과 관련된 코드가 실행되도록 해줍니다.

1. 우선 Access token이 필요합니다. Accout settings > Developer settings > Personal access tokens으로 들어갑니다. 
사진
1. Generate new token을 누르고, repo와 workflow 권한이 있는 token을 만듭니다.
1. token을 복사하고, 이번에는 repository settings로 갑니다.
Secrets에서 New repository secret을 눌러 새로운 Secret을 만듭니다.
Name은 `ACTIONS_DEPLOY_ACCESS_TOKEN`로, Value는 조금 전에 복사한 token으로 해주세요.
1. 이번에는 Actions 탭을 선택한 후, New workflow 버튼을 누릅니다. 
1. Node.js의 Set up this workflow를 선택합니다.
(사실 어차피 actions 내용은 복붙할 거라 뭘 누르든 상관 없습니다.)
1. 아래 내용을 복사해 넣습니다.
만약 master가 아닌 main 브랜치를 이용 중이라면 `master`를 `main`으로 바꿔주세요.
1. Start commit을 누르고 Commit new file을 누릅니다.
이로서 github action을 설정하는 commit이 이루어졌습니다.
`.github/workflow/node.js.yml`이라는 파일이 만들어졌을 것입니다.
로컬에서는 pull 받아주세요.

Github actions 설정까지 되었습니다.
이제 master 브랜치를 push하거나 pull_request를 merge할 때마다 해당 내용을 반영하여 배포가 될 것입니다.


<br/>

## 원리

과제2에서 우리는 index.html을 만들지 않았고, 대신 `src/` 폴더에 `index.js` 등의 파일이 위치하게 됩니다.
이를 배포에 적합하게 만들기 위해서는 `npm run build`를 실행하면 됩니다.
(궁금하면 직접 해보세요.
`build/` 폴더가 만들어질 겁니다.)

Github pages에는 이 `build/` 폴더만을 올려서 배포해야 합니다.
이를 하기 위해서는 gh-pages라는 새로운 브랜치를 만들 것입니다.

이제 수정 사항이 있을 때마다 `npm run build`를 하고,
그 결과를 gh-pages 브랜치에 적용시키고,
gh-pages 브랜치를 push해주면 됩니다.
......이 귀찮은 작업을 한 번에 해주는 것이 gh-pages 라이브러리입니다.

우리는 아까 package.json에서 `"deploy": "gh-pages -d build"`라는 줄을 추가했습니다.
이는 `npm run deploy` 명령어가 `gh-pages -d build`를 실행하도록 하겠다는 뜻입니다.
그리고 이 명령어는 React app을 build하고,
그 내용을 gh-pages 브랜치에 적용시키고, push까지 해줍니다.
그렇기에 우리는 `npm run deploy`만 시키면 되는 거죠.

Github actions는 master 브랜치가 push되었을 때 `npm run deploy` 명령어가 실행되도록 하는 역할입니다.
master 브랜치에 push가 이루어지면 우리가 node.js.yml에 입력한 일련의 명령어들이 runner라는 서버에서 실행됩니다.
제로 node.js.yml 파일에 `npm run deploy` 명령어가 적혀있음을 알 수 있습니다.

그리고 이 runner가 gh-pages 브랜치에 push를 하기 위해서는 권한이 필요합니다.
그렇기에 우리가 Access token을 발급받은 것입니다. 이 token을 github secrets에 저장해두었고,
runner에서는 이 secret을 사용합니다.
(node.js.yml에서 `git remote set-url origin https://${github_token}@github.com/${repository}` 부분)

<br/>

---
---

<br/>

여기까지 github pages로 간단히 배포하는 법에 대해 정리해보았습니다.
제가 잘못 알고 있는 내용이 있다면 알려주시면 감사하겠습니다. :heart:
