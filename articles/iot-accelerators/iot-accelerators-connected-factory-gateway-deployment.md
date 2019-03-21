---
title: Implementar o gateway de fábrica ligada - Azure | Documentos da Microsoft
description: Como implementar um gateway no Windows ou Linux para permitir a conectividade para o acelerador de solução de fábrica ligada.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 99953b486fbd1daa9800aa850684447465eead9e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182103"
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-solution-accelerator-on-windows-or-linux"></a>Implementar um gateway de borda para o acelerador de solução de fábrica ligada no Windows ou Linux

Tem dois componentes de software para implementar um gateway do edge para o *fábrica ligada* acelerador de solução:

- O *OPC Proxy* estabelece uma ligação para a fábrica ligada. O Proxy OPC, em seguida, aguarda para mensagens de comando e controle do navegador de OPC integrada, que é executado no portal da solução de fábrica ligada.

- O *o publicador OPC* liga-se a servidores OPC UA no local existentes e encaminha mensagens de telemetria dos mesmos para a fábrica ligada. Pode se conectar um dispositivo clássico OPC a utilizar o [adaptador clássico de OPC para OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md).

Ambos os componentes são de código aberto e estão disponíveis como origem no GitHub e como contentores do Docker no DockerHub:

| GitHub | DockerHub |
| ------ | --------- |
| [Publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) | [Publicador OPC](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [Proxy OPC](https://github.com/Azure/iot-edge-opc-proxy)         | [Proxy OPC](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

Não tem um endereço IP destinado ao público ou portas de entrada abertas na firewall do gateway para qualquer um dos componentes. Os componentes de OPC Proxy e o publicador OPC só utilizam a porta 443 de saída.

Os passos neste artigo mostram como implementar um gateway do edge com o Docker no Windows ou Linux. O gateway permite que a conectividade com o acelerador de solução de fábrica ligada. Também pode utilizar os componentes sem fábrica ligada.

> [!NOTE]
> Ambos os componentes podem ser utilizados como módulos no [do Azure IoT Edge](https://github.com/Azure/iot-edge).

## <a name="choose-a-gateway-device"></a>Escolha um dispositivo de gateway

Se ainda não tiver um dispositivo de gateway, a Microsoft recomenda a que comprar um gateway comercial de um dos seus parceiros. Para obter uma lista de dispositivos de gateway compatíveis com a solução de fábrica ligada, visite o [catálogo de dispositivos do IoT do Azure](https://catalog.azureiotsolutions.com/?q=opc). Siga as instruções fornecidas com o dispositivo para configurar o gateway.

Se precisar de configurar manualmente um dispositivo de gateway existente, utilize as instruções seguintes.

## <a name="install-and-configure-docker"></a>Instalar e configurar o Docker

Instale [Docker para Windows](https://www.docker.com/docker-windows) no seu dispositivo de gateway baseado no Windows ou utilizar um Gestor de pacotes para instalar o docker no seu dispositivo de gateway baseado no Linux.

Durante a configuração do Docker para Windows, selecione uma unidade em sua máquina host para partilhar com o Docker. Captura de ecrã seguinte mostra a partilha a **1!d** unidade no seu sistema Windows. Uma unidade de partilha permite o acesso à unidade de anfitrião a partir de dentro de um contentor de docker:

![Instalar o Docker para Windows](./media/iot-accelerators-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> Também pode efetuar este passo após a instalação do docker a partir da **definições** caixa de diálogo. Com o botão direito sobre a **Docker** ícone na Bandeja do sistema do Windows e escolha **definições**. Se tiverem sido implementadas atualizações importantes do Windows para o sistema, como o Windows Fall Creators update, anular a partilha as unidades e partilhá-los novamente para atualizar os direitos de acesso.

Se estiver a utilizar o Linux, não é necessária nenhuma configuração adicional para ativar o acesso ao sistema de arquivos.

No Windows, crie uma pasta no disco partilhado com o Docker, no Linux, crie uma pasta sob o sistema de ficheiros de raiz. Este passo a passo refere-se a esta pasta como `<SharedFolder>`.

Quando consultar o `<SharedFolder>` num comando Docker, certifique-se de que utilize a sintaxe correta para o seu sistema operativo. Aqui estão dois exemplos, para Windows e outro para Linux:

- Se está a utilizar a pasta `D:\shared` no Windows como seu `<SharedFolder>`, a sintaxe de comando do Docker é `d:/shared`.

- Se está a utilizar a pasta `/shared` no Linux, como seu `<SharedFolder>`, a sintaxe de comando do Docker é `/shared`.

Para obter mais informações, consulte a [utilizar volumes](https://docs.docker.com/engine/admin/volumes/volumes/) referência do motor de docker.

## <a name="configure-the-opc-components"></a>Configurar os componentes OPC

Antes de instalar os componentes OPC, conclua os seguintes passos para preparar o seu ambiente:

1. Para concluir a implementação do gateway, terá do **iothubowner** cadeia de ligação do IoT Hub na sua implementação de fábrica ligada. Na [portal do Azure](https://portal.azure.com/), navegue até ao seu IoT Hub no grupo de recursos criado quando implementou a solução de fábrica ligada. Clique em **políticas de acesso partilhado** para o acesso a **iothubowner** cadeia de ligação:

    ![Localize a cadeia de ligação do IoT Hub](./media/iot-accelerators-connected-factory-gateway-deployment/image2.png)

    Copiar o **ligação chave primária da cadeia de caracteres** valor.

1. Para permitir a comunicação entre os contentores do docker, precisa de uma rede de ponte definidas pelo utilizador. Para criar uma rede de ponte para os seus contentores, execute os seguintes comandos no prompt de comando:

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    Para verificar a **iot_edge** foi criada a rede de ponte, execute o seguinte comando:

    ```cmd/sh
    docker network ls
    ```

    Sua **iot_edge** rede de ponte está incluído na lista de redes.

Para executar o publicador de OPC, execute o seguinte comando no prompt de comando:

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.4 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- O [GitHub de publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) e o [docker run referência](https://docs.docker.com/engine/reference/run/) fornecem mais informações sobre:

  - As opções de linha de comandos do docker especificadas antes do nome de contentor (`microsoft/iot-edge-opc-publisher:2.1.4`).
  - O significado dos parâmetros da linha de comandos de publicador OPC especificado após o nome do contentor (`microsoft/iot-edge-opc-publisher:2.1.4`).

- O `<IoTHubOwnerConnectionString>` é o **iothubowner** partilhado a cadeia de ligação de política de acesso do portal do Azure. Esta cadeia de ligação que copiou no passo anterior. Só precisa desta cadeia de ligação para a primeira execução de publicador OPC. Em execuções subsequentes, deve omita-porque ela apresenta um risco de segurança.

- O `<SharedFolder>` que utiliza e sua sintaxe é descrita na secção [instalar e configurar Docker](#install-and-configure-docker). O publicador OPC utiliza o `<SharedFolder>` para:

    - Ler e escrever no ficheiro de configuração de publicador OPC.
    - Escreva no ficheiro de registo.
    - Disponibilize ambos estes ficheiros fora do contentor.

- O publicador OPC lê a respetiva configuração a partir da **publishednodes.json** arquivo, que é ler e escrito para o `<SharedFolder>/docker` pasta. Este ficheiro de configuração define os dados do nó OPC UA num determinado servidor OPC UA que o publicador de OPC deverá subscrever. A sintaxe completa da **publishednodes.json** descrito no ficheiro a [o publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) página no GitHub. Quando adicionar um gateway, colocar vazio **publishednodes.json** para a pasta:

    ```json
    [
    ]
    ```

- Sempre que o servidor OPC UA notifica o publicador OPC de uma alteração de dados, o novo valor é enviado para o IoT Hub. Consoante as definições de criação de batches, o publicador de OPC talvez acumular-se os dados antes de enviar os dados para o IoT Hub num batch.

- Docker não suporta a resolução de nomes NetBIOS, resolução de nomes DNS única. Se não tiver um servidor DNS na rede, pode utilizar a solução mostrada no exemplo anterior da linha de comandos. O exemplo de linha de comando anterior utiliza o `--add-host` parâmetro para adicionar uma entrada no ficheiro de anfitriões de contentores. Esta entrada permite a pesquisa de nome de anfitrião para o determinado `<OpcServerHostname>`, resolução para o endereço IP indicado `<IpAddressOfOpcServerHostname>`.

- OPC UA que usa certificados X.509 para autenticação e encriptação. Coloque o certificado de publicador OPC no servidor OPC UA que está a ligar, para se certificar de que o publicador OPC de confianças de entidades. O arquivo de certificados de publicador OPC está localizado no `<SharedFolder>/CertificateStores` pasta. Pode encontrar o certificado de publicador OPC na `trusted/certs` pasta na `CertificateStores` pasta.

  Os passos para configurar o servidor OPC UA dependem do dispositivo que estiver a utilizar. estes passos são normalmente documentados no manual de utilizador do servidor OPC UA.

Para instalar o Proxy OPC, execute o seguinte comando no prompt de comando:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

Apenas terá de executar a instalação de uma vez num sistema.

Utilize o seguinte comando para executar o Proxy OPC:

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.4 -D /mapped/cs.db
```

O Proxy OPC guarda a cadeia de ligação durante a instalação. Em execuções subseqüentes, deve omitir a cadeia de ligação, porque ela apresenta um risco de segurança.

## <a name="enable-your-gateway"></a>Ativar o seu gateway

Conclua os passos seguintes para ativar o gateway no acelerador de solução de fábrica ligada:

1. Quando os dois componentes estão em execução, navegue para o **ligar o seu próprio servidor OPC UA** página no portal da solução de fábrica ligada. Esta página apenas está disponível para administradores na solução. Introduza o URL de ponto final do publicador (opc.tcp://publisher: 62222) e clique em **Connect**.

1. Estabelece uma relação de confiança entre o publicador OPC e o portal de fábrica ligada. Quando vir um aviso de certificado, clique em **continuar**. Em seguida, verá um erro que o publicador de OPC não confiam o cliente Web de UA. Para resolver este erro, copie os **cliente da Web de UA** certificado do `<SharedFolder>/CertificateStores/rejected/certs` pasta para o `<SharedFolder>/CertificateStores/trusted/certs` pasta no gateway. Não precisa de reiniciar o gateway.

Agora pode ligar ao gateway a partir da cloud e está pronto para adicionar servidores OPC UA à solução.

## <a name="add-your-own-opc-ua-servers"></a>Adicionar seus próprios servidores OPC UA

Para adicionar seus próprios servidores OPC UA para o acelerador de solução de fábrica ligada:

1. Navegue para o **ligar o seu próprio servidor OPC UA** página no portal da solução de fábrica ligada.

    1. Inicie o servidor OPC UA que quer ligar. Verifique se o seu servidor OPC UA pode ser contactado a partir de publicador OPC e o Proxy OPC em execução no contentor. Veja os comentários anteriores sobre a resolução de nomes.
    1. Introduza o URL de ponto final do seu servidor OPC UA (`opc.tcp://<host>:<port>`) e clique em **Connect**.
    1. O processo de configuração de ligação estabelece uma relação de confiança entre o portal de fábrica ligada (cliente de OPC UA) e o servidor OPC UA que está a tentar ligar. No dashboard de fábrica ligada, obterá uma **não é possível verificar o certificado do servidor que pretende ligar** aviso. Quando vir um aviso de certificado, clique em **continuar**.
    1. Mais difícil de configuração é a configuração do certificado do servidor OPC UA que está a tentar ligar a. Para servidores OPC UA baseados em PC, pode conseguir uma caixa de diálogo de aviso no dashboard do que pode reconhecer. Para sistemas de servidor OPC UA incorporados, consulte a documentação do seu servidor OPC UA para procurar como esta tarefa é feita. Para concluir essa tarefa, poderá ter o certificado de cliente de OPC UA no portal de fábrica ligada. Um administrador pode transferir este certificado no **ligar o seu próprio servidor OPC UA** página:

        ![Portal de solução](./media/iot-accelerators-connected-factory-gateway-deployment/image4.png)

1. Procure a árvore de nós de OPC UA do seu servidor OPC UA, com os nós do OPC que pretende enviar valores para a fábrica ligada e selecione o botão direito **publicar**.

1. Agora fluxos de telemetria do dispositivo de gateway. Pode ver a telemetria no **localizações de fábrica** vista do portal de fábrica ligada, sob **nova fábrica de**.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a arquitetura do solution accelerator Connected Factory, veja [instruções do acelerador de solução de fábrica ligada](iot-accelerators-connected-factory-sample-walkthrough.md).

Saiba mais sobre o [implementação de referência de publicador OPC](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher).