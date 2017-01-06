# Colocando seus testes no CI com Circle CI

Continuos Integration, vulgo CI vem sendo um assunto muito comentado, principalmente em algumas rodas de QAs e logo de cara as pessoas pensam em Jenkins para subir os testes e deixar bonitinho, com relatórios exorbitantes e bonitos pra ganhar aquela moral com o time e claro, pra facilitar o trabalho.

Mas, muitos QAs quando se deparam com o Jenkins e sua dificuldade em configurar e entender os conceitos, acabam construindo muros e então criando o desânimo e deixam de lado essa cultura de OPs que é muito legal.

Com esse intuito, comecei a conversar com alguns amigos meus (devs e devops) sobre o que a galera moderna anda utilizando e vieram com um app chamado Circle Ci (https://circleci.com), que na minha opinião, facilitou MUITO o trabalho de subir os testes em uma ferramenta de entrega contínua, e não precisamos perder tempo instalando o Jenkins, configurandos os plugins, bla bla bla de chatice e no caso do circleci, login via github e o mais legal, tudo se resume a apenas um arquivo =).

Com isso, resolvi criar esse repositório para justamente passar essa visão simplista de como subir seus testes em uma ferramenta de CI sem muito custo, sem muitos adicionais rsrsr, de forma rápida e clara. Nesse repositório vou ensinar apenas como colocar os testes lá no circleci para que todo push que você der nos seu repositório possa pegar esses testes e executá-los, pois a idéia do circle é exatamente essa, comitt -> push -> executa.

Bem, nesse repositório, que está escrito em cucumber + capybara, não vou aqui explicar nada sobre cucumber, nada sobre capybara, se quiserem, vejam [capybara for all] (https://github.com/thiagomarquessp/capybaraforall), ou seja, estou considerando que todos aqui conheçam, ou indo além, que tenham seus testes no github. Nesse exemplo, vou colocar os testes que estão em linguagem ruby, MAS, se vocês tiverem seus testes em: Go (Golang), Haskell, Java, Node.js, PHP, Python, Scala o conceito será o mesmo OK.

Só para explicar o repositório em questão OK: Está em cucumber com apenas uma feature chamada Cadastro no Groupon e um arquivo de steps com o mesmo nome, assim como todo o conceito de Page Objects com Site::Prism, etc. No arquivo de feature, tem uma variável que antecede o Cenario de cadastro chamada @gp_cadastro que vamos utilizá-la como TAG para execução do meu teste OK.

Bom, vamos lá ao que interessa:

Se vocês observarem o repositório, temos ai um arquivo na raiz do projeto chamado circle.yml dessa maneira:

```ruby
machine:
 ruby:
   version: ruby-2.2.2
 services:
   - docker
dependencies:
 pre:
   - sudo apt-get install libxss1 libappindicator1 libindicator7
   - wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
   - sudo dpkg -i google-chrome*.deb
   - sudo apt-get install -f
   - sudo apt-get install jq nodejs curl -qy
   - npm install -g geckodriver
   - npm install -g chromedriver@2.25
   - gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
   - curl -sSL https://get.rvm.io | bash -s stable --ruby=2.2.2
 override:
   - rvm use .
   - gem install bundler
   - bundle install
test:
 override:
   - cd cucumber; cucumber -p chrome -p qa --tags @gp_cadastro
```
