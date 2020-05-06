# Bluetooth sem PulseAudio
# 06/05/2020

Na versão 5 do BlueZ o suporte ao Alsa foi retirado, deixando todos reféns do PulseAudio para utilizar fones ou caixas bluetooth.
PulseAudio nunca foi necessário para o meu caso pois sempre usei ligação direta em caixas de som.

Antes de instalar mais uma camada de abstração no meu laptop para usar algo que sempre foi possível antes, resolvi ao menos perder alguns minutos (ou horas) pesquisando se ainda existe a possibilidade de ficar somente com o Alsa.

Foi nessa busca que encontrei o [BlueZ-Alsa](https://github.com/Arkq/bluez-alsa). O projeto foi criado para manter a integração entre Alsa e BlueZ que foi retirada com o lançamento da versão 5.
Como estou utilizando a distribuição Void Linux, o artigo será focado na mesma, porém adaptando alguns detalhes deve funcionar normalmente em qualquer uma.

Primeiro será necessário instalar os pacotes bluez e bluez-alsa e ativar seus serviços com na inicialização do sistema. Void Linux não utiliza systemd, portando os comandos são diferentes, mas no caso de outras distribuições basta pesquisar como habilitar os serviços.

    ln -s /etc/sv/bluetoothd /var/service/
    ln -s /etc/sv/bluez-alsa /var/service/
    
Adicione seu usuário ao grupo bluetooth.

    usermod -a -G bluetooth usuário

Eu recomendo agora que reiniciem a interface gráfica (Xorg) ou o computador para que a inclusão no grupo tenha efeito em todo o sistema.

Antes de prosseguir, confirme que o bluetooth não está bloqueado.

    rfkill unblock bluetooth

Talvez seja necessário executar o commando acima como root.

Agora iremos escanear e conectar ao dispositivo bluetooth para ver se tudo está funcionando.

No terminal, execute o programa "bluetoothctl". Você entrará no prompt do programa. Agora será necessário escanear e conectar a caixa de som ou fone de ouvido. Execute os seguintes comandos:

    agent on	# para habilitar o agente
    power on	# para ligar o controlador, pois ele inicia desligado por padrão.
    scan on     # para escanear os dispositivos disponíveis caso o que você queira não esteja na lista
    devices	# para listar o endereço MAC dos dispositivos bluetooth disponíveis

Agora iremos parear o dispositivo e conectar. Pode utilizar a tecla "TAB" para autocompletar, assim não é necessário digitar o endereço MAC completo.

    pair ENDEREÇO_MAC
    connect ENDEREÇO_MAC
    exit

O prompt deve responder "Connection successful". Agora o dispositivo está conectado.

Será necessário criar o arquivo ".asoundrc" no diretório raiz do seu usuário (/home/usuário) configurando um nome para o dispositivo bluetooth, assim os programas poderão se conectar. Crie o arquivo com o seguinte:

    pcm.bluetooth {
    	type plug
    	slave.pcm {
    	    type bluealsa
    	    device "03:EB:ED:AE:92:1C" # endereço MAC do dispotivo
    	    profile "a2dp"
    	}
    }

No lugar de "pcm.bluetooth" recomendo utilizar o nome do seu dispositivo, pois será a referência para se conectar. No meu caso, a caixa de som é da marca Anker, portando coloquei "pcm.anker".

Reinicie o computador para que o sistema aplique as configurações que inserimos no arquivo ".asoundrc".

Caso reinicie, será necessário executar o "bluetoothctl" novamente e se conectar ao dispositivo.

Agora é só abrir o seu player de música e configurar a saída de som para o dispositivo bluetooth.

O problema deste método é ter que trocar a saída de som de cada aplicativo que for utilizar para direcionar ao bluetooth e consequentemente ter que reverter quando não for mais utilizar.

Com o PulseAudio e um aplicativo como o Blueman, as coisas ficam muito mais simples. Assim que conectado, a saída é redirecionada por padrão.

O Alsa não funciona muito bem neste tipo de caso. Encontrei alguns hacks para fazer isso funcionar, mas nada satisfatório.

Caso fazer essa mudança manual seja um problema, infelizmente a melhor opção é jogar a toalha e instalar o PulseAudio. No final das contas, foi o que eu fiz.

[< Voltar](/index.html)
