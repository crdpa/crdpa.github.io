# Configurando bspwm e dois monitores
## 18/06/2020

Preciso configurar meu gerenciador de janelas para utilizar dois monitores e por um bom tempo venho empurrando isso com a barriga utilizando somente o monitor do laptop ou só o monitor externo.

Hoje resolvi mudar isso e documentar aqui toda a empreitada.

Meu desktop padrão contém oito áreas de trabalho. Quando utilizar dois monitores, quero que as quatro primeiras áreas de trabalho fiquem no monitor externo e o restante no monitor do laptop.

O comando para ativar o monitor externo, definir como primário e colocar ao lado esquerdo do meu laptop é

    xrandr --output HDMI1 --primary --left-of eDP1 --auto

Assim, quando eu saio com o mouse para a esquerda do monitor do laptop, ele aparece no monitor externo.

Bspwm é um gerenciador de janelas configurado por comandos externos utilizando o cliente bspc e seu arquivo de inicialização é um shell script, portanto é possível fazer infinitas customizações. Criei uma condição que detecta se dois monitores estão definidos logo após iniciar o gerenciador. Caso seja encontrado dois monitores, é executada a configuração para dois, senão a configuração é feita para um monitor somente.

``` {.bash}
# checa se existem dois monitores definidos
MON=$(xrandr --listmonitors | grep Monitors | cut -b 11-)

# caso tenha, executa o setup para dois
if [[ $MON == 2 ]] ; then
    xrandr --output HDMI1 --primary --left-of eDP1 --auto &
    bspc monitor HDMI1 -d 1 2 3 4
    bspc monitor eDP1 -d 5 6 7 8
else
    bspc monitor eDP1 -d 1 2 3 4 5 6 7 8
fi
```
    
O setup para dois monitores define quatro áreas de trabalho para cada monitor, ficando as áreas cinco a oito no laptop.

Agora é hora de criar um script para a barra de informações (polybar). Eu quero que apareçam uma barra diferente em cada monitor. Toda a informação necessária como carga da bateria, relógio, conexão de internet e temperatura ficam no monitor principal. No caso de estar utilizando só o notebook, quero as informações nele, mas se estiver com o monitor externo, quero as informações no monitor externo e a barra no laptop só com o nome das áreas de trabalho.

No script de execução do polybar, bastou colocar isso:

``` {.bash}
# termina qualquer instância do programa que esteja rodando 
pkill polybar
 
# variável guarda a quantidade de monitores
MONS=$(polybar --list-monitors | wc -l)

# caso tenha dois, execute as duas barras
# A bar1 é a barra com mais informações. Ela irá para o monitor
# externo (HDMI1) caso ele esteja conectado.
if [[ "$MONS" == "2" ]] ; then
    MON1=HDMI1 polybar --reload bar1 &
    MON2=eDP1 polybar --reload bar2 &
else
    MON1=eDP1 polybar --reload bar1 &
fi
```

O resultado ficou satisfatório, mas existe um problema. Caso eu resolva desconectar ou conectar o monitor externo com o BSPWM rodando, tudo vira uma bagunça.

Não é algo que faço. Ou ligo o computador com o monitor externo já conectado ou não. Caso venha a fazer de outra maneira, reinicio o Xorg. Pelo pouco que olhei, seria necessário monitorar eventos no udev e reagir. Fiz algumas tentativas, mas as coisas ficaram meio bizarras.

Se no futuro isso se tornar um problema, pesquiso como resolver e crio outro artigo aqui.

Screenshot obrigatório do setup concluído:


[![](/blog/2020/0618tiny.png)](/blog/2020/0618.png)
