# Construindo um site com bash e pandoc
## 23/04/2020
Há tempos venho alimentando a idéia de criar um site pequeno pra escrever textos com informações que adquiro ao longo do tempo, mas sempre acabo deixando de lado.
Agora, em meio a essa pandemia e isolamento, resolvi deixar a preguiça de lado.

Como não quero nada muito complexo e aproveitando para praticar um pouco mais de shell script, optei por utilizar [pandoc](https://pandoc.org/) para converter markdown em html e criar dois scripts automatizando todo o processo.

O pandoc me permite converter arquivos markdown em html sem gerar uma página completa, me deixando apenas com o conteúdo que fica entre as tags <body></body> do arquivo html.

Decidi criar um arquivo para o topo da página chamado top.html onde escreveria o menu geral e o meu script escreveria o início do documento html, concatenaria o arquivo top.html, depois o arquivo html da página principal e por fim escreveria o final do código html, fechando o código.

Criei duas funções que são utilizadas após converter o arquivo em html. A função header cria o início da página baseada no nome do arquivo que informei no comando e a função bottom fecha as tags necessárias.

    header() {
        echo "<!DOCTYPE html>" > $filename.html
        { echo "<html>"; echo "<head>"; } >> $filename.html
        if [ -z "$1" ]; then
            echo "No title supplied. Using name of the file." >&2
            echo "  <title>$filename</title>" >> $filename.html
        else
            echo "  <title>$1</title>" >> $filename.html
        fi
        { echo "  <link rel=\"stylesheet\" href=\"styles.css\">"; \
        echo "</head>"; echo "<body>"; echo ""; } >> $filename.html
    }
    
    bottom() {
        { echo "</body>"; echo "</html>"; } >> $filename.html
    }

O funcionamento é simples. O script é invocado informando um ou dois argumentos. O primeiro é o arquivo markdown a ser convertido em html e o segundo é o título que a página terá. Se o título não for informado, o script passa o nome do arquivo como tĩtulo.
É algo bem simples, mas por enquanto está servindo bem ao propósito.

O próximo passo foi criar outro script para automatizar o índice da seção [blog](/blog.html) do site. Sempre  que eu escrever algo novo é só rodar o script para gerar um novo índice.
Para facilitar o processo, a hierarquia de diretórios ficou como blog/ano e o nome do arquivo é MÊSDIA - Titulo do texto.md, ficando blog/2020/0423 - Construindo um site com bash e pandoc.md no caso deste texto.

O script executa um loop passando pelos diretórios e seus arquivos gerando uma linha para cada arquivo html contido na pasta e formatando como lista em um arquivo markdown.

Segue o trecho do loop:

    if [ -d "$SRC/blog" ]; then
        for f1 in "$SRC/blog/"*; do
           nf="${f1: -4}"
           for f2 in "$SRC/blog/$nf/"*.md;
               l2a=$(echo $f2 | sed 's:.*/::')
               l2b="${l2a::-5}"
               printf -- "- %s/[%s](blog/%s/%s)\n" "$nf" "$l2b" "$nf" "$l2a"
           done
        done
        tac $tmp > $filename
    else
        exit 1
    fi

Primeiro o trecho checa se o diretório blog/ existe e executa um loop em cada diretório existente dentro.

Como o processo retorna o caminho absoluto (dir1/dir2/.../blog/2020) eu utilizo

    nf="${f1: -4}"

para manter os últimos 4 caracteres do resultado, no caso os anos (2019, 2020).

Após isso outro loop se inicia ciclando dentro do diretório de cada ano (variável nf) coletando o resultado, que também retorna caminho absoluto (dir1/.../blog/2020/0423 - Nome.html), portanto utilizo sed para remover todos os caracteres até a última "/", incluindo esta, resultando em 0423 - Nome.html onde atribuo a variável l2a.

Por fim crio outra variável com o nome do arquivo utilizando

    l2b="${l2a::-5}"

e com o ::-5 removo os últimos 5 caracteres (.html) para utilizar como texto no índice.

O comando printf escreve tudo formatado:

    printf -- "- %s/[%s](blog/%s/%s)\n" "$nf" "$l2b" "$nf" "$l2a"

Criando um índice de formato markdown, precedido por hífen.

No final do processo utilizo o comando tac (o inverso de cat) para inverter a ordem fazendo com que as postagens mais recentes fiquem no topo, deixando o índice melhor organizado.

Deixei tudo muito simples e o formato final você já visualizou antes de entrar nesta página.

Você pode ver e baixar os scripts no meu [repositório](https://github.com/crdpa/bsgs).
