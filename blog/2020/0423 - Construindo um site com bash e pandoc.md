# Construindo um site com bash e pandoc
## 23/04/2020
Há tempos venho alimentando a idéia de criar um site pequeno pra reunir idéias e informações que adquiro com o tempo, mas sempre acabo deixando de lado.
Agora, em meio a essa pandemia e isolamento, resolvi deixar a preguiça de lado.

Como não quero nada muito complexo e aproveitando para praticar um pouco mais de shell script, optei por utilizar [pandoc](https://pandoc.org/) para converter markdown em html e criar dois scripts automatizando todo o processo.

O que eu quero é que todas as páginas apresentassem o mesmo topo com links para a página inicial e outro para os textos que irei escrever ao longo do tempo.

O pandoc me permite converter arquivos markdown em html sem gerar uma página completa, me deixando apenas com o conteúdo que iria entre as tags <body></body> do arquivo html.

Decidi em criar um arquivo para o topo da página chamado top.html onde escreveria o menu geral e o meu script escreveria o início do documento html, concatenaria o menu, depois o arquivo html da pãgina principal e por fim escreveria o final do código html. Para ficar mais fácil criei duas funções.

A função header da início a página html baseada no nome do arquivo html que informei no comando e a função fecha as tags necessárias.

    header() {
        echo "<!DOCTYPE html>" > $filename.html
        { echo "<html>"; echo "<head>"; } >> $filename.html
        if [ -z "$1" ]; then
            echo "No title supplied. Using name of the file." >&2
            echo "  <title>$filename</title>" >> $filename.html
        else
            echo "  <title>$1</title>" >> $filename.html
        fi
        echo "  <link rel=\"stylesheet\" href=\"styles.css\">" >> $filename.html
        { echo "</head>"; echo "<body>"; echo ""; } >> $filename.html
    }
    
    bottom() {
        { echo "</body>"; echo "</html>"; } >> $filename.html
    }

O funcionamento é simples. O script é invocado informando um ou dois argumentos. O primeiro é o arquivo markdown a ser convertido em html e o segundo é o título que a página terá. Se o título não for informado, o script passa o nome do arquivo como tĩtulo.
É algo bem simples, mas por enquanto está servindo bem ao propósito.

O próximo passo foi criar outro script para automatizar o índice da seção [blog](/blog.html) do site. Cada vez que eu escrever algo novo é só rodar o script para gerar um novo índice.
Para facilitar o processo, a hierarquia de diretórios ficou como blog/ano e o nome do arquivo seria MESDIA - Titulo do texto.md, ficando blog/2020/0426 - Construindo um site com bash e pandoc.md no caso deste texto.

O script executa um loop passando pelos diretórios e seus arquivos gerando uma linha para cada arquivo html contido na pasta e formatando como lista em um arquivo markdown.

Segue o trecho do loop:

    if [ -d "$SRC/blog" ]; then
        for f1 in "$SRC/blog/"*; do
           nf="${f1: -4}"
           for f2 in "$SRC/blog/$nf/"*.md; do
               l2a=$(echo $f2 | sed 's:.*/::')
               printf -- "- [%s/%s](blog/%s)\n" "$nf" "$l2a" "$l2a"
           done
        done
        tac $tmp > $filename
    else
        exit 1
    fi

No final do processo utilizo o comando tac (o inverso de cat) para inverter a ordem fazendo com que as postagens mais recentes fiquem no topo, deixando o índice melhor organizado.

Deixei tudo muito simples e o formato final você já visualizou antes de entrar nesta página.

Você pode ver e baixar os scripts no meu [repositório](https://github.com/crdpa/bsgs).
