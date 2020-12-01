# A crise do www e a pequena internet
## 30/11/2020

Para quem utiliza computadores e a internet desde os primórdios e viveu na era do IRC, ICQ e até antes, se lembra de como a internet era naquele tempo.

O fato da tecnologia ainda estar no início e ter certas restrições, fazia com que a criatividade aflorasse mais e, principalmente, tornava o ato de navegar na internet mais prazeiroso.

A tecnologia evoluiu demais desde aquela época e hoje os sites fazem muito mais coisas do que só exibir imagens e textos, mas essas coisas são realmente necessárias?

Virou lugar comum hoje em dia uma página com o objetivo de exibir um texto possuir 2 ou 3 megabytes. [Este texto](https://medium.com/the-delighted-blog/bloat-6bccea185816) de 400 palavras requer 1.2 megabytes. [Anna Karenina](https://www.gutenberg.org/files/1399/1399-0.txt) do Tolstoy requer 2 megabytes. O livro possui por volta de 800 páginas e 350 mil palavras. Tem algo muito errado acontecendo na internet hoje em dia.

Essas páginas são um problema nos computadores, mas são piores ainda em smartphones.

Por que, com os computadores cada vez mais potentes, parece que a internet não fica mais rápida?

Scripts, vigilância e publicidade. Agora todo website quer saber como você chegou lá, em que ponto da página sua barra de rolagem está, quantos cliques você dá, oferece produtos, coleta informações e outras mil e uma funções que você não vê diretamente. Não é a toa que o uso de bloqueadores de propaganda e scripts cresce exponencialmente. A internet virou um território hostil, um campo minado onde você não se sente mais no controle e agora é guiado por terceiros querendo sua atenção.

Existem inúmeros artigos falando sobre esse assunto e eu não me considero mais experiente do que essas pessoas para falar mais. O motivo desse texto é apresentar uma alternativa que vem lentamente crescendo para escapar de todo esse caos. Esse novo espaço é conhecido como a pequena internet.

## A pequena internet

Gopher é um protocolo que foi muito utilizado nos anos 90 e foi suplantado pelo HTTP (a internet de hoje). O protocolo é mais voltado para servir textos e, ainda hoje, existem muitas pessoas que segue utilizando o protocolo para hospedar suas páginas de internet.

Mais recentemente outro protocolo foi criado. Seu nome é Gemini. O protocolo fica em um meio termo entre o Gopher e o HTTP. Ele ainda é texto puro, mas tem sua própria linguagem de marcação (markup language) similar ao markdown para uma melhor formatação dos textos. Porém, quem dita como o texto será exibido é o navegador cliente, não o dono da página.

Gemini também tem como objetivo ser mais seguro que o Gopher, que não possui nenhuma criptografia. Gemini usa TLS para criptografar o tráfego entre o servidor e o cliente e também possui alguns outros avanços em relação ao Gopher, porém não chega nem perto de ser o monstro gigante que é a internet moderna.

O protocolo continua simples, leve e focado. A maior vantagem: não é possível servir scripts.

Hoje em dia existem vários servidores Gemini. Grande parte dessas comunidades também servem páginas Gopher e HTTP. Geralmente não são servidores comerciais e impessoais como é o costume na grande internet, mas sim comunidades de usuários entusiastas. Como são mantidos por pessoas comuns e não empresas, a hospedagem tende a ser limitada em capacidade não só de megabytes, mas também de usuários.

A [comunidade onde hospedo a minha](https://republic.circumlunar.space/) oferece 500MB de hospedagem para cada usuário. O que é uma quantia enorme para servir texto. Você pode dizer muito com todo esse espaço.

Acessar a internet pequena não é tão simples quanto abrir o navegador e digitar um endereço. Na verdade, é, mas você precisará de um navegador diferente. Também tem como acessar pelo seu navegador atual utilizando um proxy, mas é mais limitado. Serve para ver um pouco antes de entrar mais de cabeça nesse mundo. [Vulpes](https://proxy.vulpes.one/gemini/gemini.circumlunar.space/capcom/) é um que tem uma interface mais bonita. A página que linkei é a CAPCOM, uma espécie de agregadora de sites Gemini.

Se quiser utilizar a pequena internet da maneira "correta", recomendo instalar um navegador. No Linux, eu utilizo o [Amfora](https://github.com/makeworld-the-better-one/amfora), que é um navegador que funciona no terminal. Se quiser algo com interface gráfica, recomendo o [Castor](https://sr.ht/~julienxx/Castor/) ou [Kristall](https://github.com/MasterQ32/kristall). Todos funcionam em Linux, Window e Mac. Exclusivo para Windows existe o [Geminaut](https://www.marmaladefoo.com/pages/geminaut).

Depois de instalado, é só abrir o navegador e digitar na barra de endereços o site que você quer visitar. Para começar, recomendo o agregador CAPCOM: gemini://gemini.circumlunar.space/capcom/

Como foi falado, todo o conteúdo é texto. Portanto o que você mais encontrará serão blogs, idéias e artigos. Quando alguém quer fornecer imagens ou arquivos, os mesmos são servidos por meio de links para download.

A pequena internet é bem pequena, mas como é formada por comunidades unidas, então tende a ser bem amigável. Qualquer pessoa é muito bem vinda.

Existe muito mais coisas que eu não falei aqui, como os pubnixes e o tildeverso, que também fazem parte da pequena internet e estão bem relacionadas, mas o assunto é bem extenso e esse texto já serve  pra se ter uma idéia de como é uma parte da pequena internet e espalhar mais sobre a sua existência.
