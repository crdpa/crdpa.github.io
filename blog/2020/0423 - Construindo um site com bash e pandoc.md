# Construindo um site com bash e pandoc
## 30/04/2020
Há tempos venho alimentando a idéia de criar um site pequeno para escrever textos com informações que adquiro ao longo do tempo, mas sempre acabo deixando de lado.
Agora, em meio a essa pandemia e isolamento, resolvi deixar a preguiça de lado.

Como não quero nada muito complexo e, aproveitando para praticar um pouco mais de shell script, optei por utilizar [pandoc](https://pandoc.org/) para converter markdown em html e criar um script automatizando todo o processo.

O pandoc me permite converter arquivos markdown em html sem gerar uma página completa, me deixando apenas com o conteúdo que fica entre as tags \<body\>\</body\> do arquivo html.

Decidi criar um arquivo para o topo da página chamado "top.md" onde consta o conteúdo da página que fica antes do índice do blog e o arquivo "bottom.md" com o conteúdo que fica após o índice. O script checa por subdiretórios e arquivos dentro do diretório blog, gera um índice e após isso concatena os três arquivos (top.md, blog.md e bottom.md) gerando um arquivo só que será convertido em html.

Duas funções foram criadas. Uma para escrever o início do documento html e outra para fechar o código da página. Não era necessário utilizar funções, pois são invocadas apenas uma vez. Fiz para facilitar a visualização e manutenção.

Para facilitar o processo, a hierarquia de diretórios é blog/ano/ e o nome do arquivo é "MÊSDIA - Titulo do texto.md", ficando "blog/2020/0430 - Construindo um site com bash e pandoc.md" no caso deste texto.

O script executa um loop passando pelos diretórios e checa se existe um arquivo .md e outro .html com o mesmo nome. Caso não exista, ele converte o arquivo markdown em html. Após esta checagem, é gerada uma linha para cada arquivo .md contido no diretório e formatado como lista em um arquivo temporário "tmp.md" em markdown.

Segue o trecho do loop:

    blog () {
            if [ -f "$f2" ] && [ ! -f "${f2::-3}.html" ]; then
                pandoc -o "${f2::-3}.html" "$f2"
            fi
            l1=$(echo "${f2::-3}" | sed 's:.*/::')
            printf -- "- %s/[%s](%s)\n" "$y" "$l1" "${f2::-3}.html" >> tmp.md
    }
    
    for f1 in "blog/"*; do
        y="${f1: -4}"
        for f2 in "blog/$y/"*.md; do
            blog
        done
    done

O loop "for" do trecho acima checa o conteúdo do diretório blog. Seu conteúdo são subdiretórios referentes aos anos. A variável "y" guarda o valor (2019, 2020, ...) e outro loop "for" é invocado dentro do diretório do ano (blog/$y), checa por arquivos .md e executa a função "blog" para cada arquivo .md encontrado.
Esta é a função que checa se existe um arquivo .md e outro .html com o mesmo nome e gera o .html caso não exista. Após isso é criada uma linha de índice em markdown com o nome e link para o artigo em um arquivo temporário "tmp.md".

Algo interessante em bash é a seguinte sintaxe:

    y="${f1: -4}"

Isso faz com que seja utilizado somente os 4 últimos caracteres da string. No caso a string é "blog/2020", mas a variável guarda somente o valor "2020".

Caso queira o resultado inverso, removendo os caracteres, basta acrescenter dois pontos. É o que faço aqui: 

    "${f2::-3}"

O "::-3" remove os últimos 3 caracteres (.md), ficando somente com o nome do arquivo, sem sua extensão.

O comando printf escreve tudo formatado em índice markdown precedido por hífen.

    printf -- "- %s/[%s](%s)\n" "$y" "$l1" "${f2::-3}.html" >> tmp.md

No final do processo utilizo o comando tac (o inverso de cat) para inverter o conteúdo do arquivo "tmp.md", gerando o "blog.md" com as postagens mais recentes no topo, deixando o índice melhor organizado.

Tudo é concatenado em um arquivo "index.md" e convertido em "tmp.html", gerando todo o conteúdo visível da página inicial. A função "header" gera o início código html, o arquivo "tmp.html" é inserido após esse código e a função "bottom" fecha o código, finalizando o arquivo "index.html". Por fim, o arquivo "tmp.html" é removido.

O resultado final você já visualizou antes de entrar nesta página.

Você pode visualizar e baixar o script no [repositório](https://github.com/crdpa/bsg).
