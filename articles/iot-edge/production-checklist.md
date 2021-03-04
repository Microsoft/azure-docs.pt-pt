---
title: Prepare-se para implementar a sua solução na produção - Azure IoT Edge
description: Aprenda a tomar a sua solução Azure IoT Edge do desenvolvimento para a produção, incluindo a configuração dos seus dispositivos com os certificados apropriados e a elaboração de um plano de implementação para futuras atualizações de código.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 65710047d5d5d1cc6b835144f7778392fb20b797
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042271"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Prepare-se para implementar a sua solução IoT Edge na produção

Quando estiver pronto para levar a sua solução IoT Edge do desenvolvimento para a produção, certifique-se de que está configurada para o desempenho contínuo.

A informação fornecida neste artigo não é todas iguais. Para ajudá-lo a priorizar, cada secção começa com listas que dividem o trabalho em duas secções: **importante** para completar antes de ir para a produção, ou **útil** para que você saiba.

## <a name="device-configuration"></a>Configuração do dispositivo

Os dispositivos IoT Edge podem ser qualquer coisa, desde um Raspberry Pi até um portátil até uma máquina virtual a funcionar num servidor. Pode ter acesso ao dispositivo fisicamente ou através de uma ligação virtual, ou pode ficar isolado por longos períodos de tempo. Seja como for, tens de ter a certeza que está configurado para funcionar adequadamente.

* **Importante**
  * Instalar certificados de produção
  * Tenha um plano de gestão de dispositivos
  * Use Moby como motor de contentor

* **Útil**
  * Escolha o protocolo a montante

### <a name="install-production-certificates"></a>Instalar certificados de produção

Todos os dispositivos IoT Edge em produção precisam de um certificado de certificado de dispositivo (CA) instalado nele. Esse certificado de CA é então declarado para o tempo de execução IoT Edge no ficheiro config. Para cenários de desenvolvimento e teste, o tempo de execução IoT Edge cria certificados temporários se não forem declarados certificados no ficheiro config. No entanto, estes certificados temporários expiram após três meses e não são seguros para cenários de produção. Para cenários de produção, deve fornecer o seu próprio certificado de AA do seu próprio dispositivo, seja a partir de uma autoridade de certificado auto-assinado ou adquirido a uma autoridade de certificados comerciais.

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Atualmente, uma limitação no libiothsmo impede a utilização de certificados que expirem em ou após 1 de janeiro de 2038.

:::moniker-end

Para compreender o papel do certificado CA do dispositivo, consulte [como a Azure IoT Edge utiliza certificados](iot-edge-certs.md).

Para obter mais informações sobre como instalar certificados num dispositivo IoT Edge e faz referência a partir do ficheiro config, consulte [o certificado Manage num dispositivo IoT Edge](how-to-manage-device-certificates.md).

### <a name="have-a-device-management-plan"></a>Tenha um plano de gestão de dispositivos

Antes de colocar qualquer dispositivo em produção, deve saber como vai gerir futuras atualizações. Para um dispositivo IoT Edge, a lista de componentes a atualizar pode incluir:

* Firmware do dispositivo
* Bibliotecas do sistema operativo
* Motor de contentor, como Moby
* IoT Edge
* Certificados da AC

Para obter mais informações, consulte [atualizar o tempo de execução do IoT Edge](how-to-update-iot-edge.md). Os métodos atuais de atualização do IoT Edge requerem acesso físico ou SSH ao dispositivo IoT Edge. Se tiver muitos dispositivos para atualizar, considere adicionar os passos de atualização a um script ou utilizar uma ferramenta de automação como o Ansible.

### <a name="use-moby-as-the-container-engine"></a>Use Moby como motor de contentor

Um motor de contentor é um pré-requisito para qualquer dispositivo IoT Edge. Apenas o motor moby é suportado na produção. Outros motores de contentores, como o Docker, trabalham com o IoT Edge e não há problema em usar estes motores para o desenvolvimento. O motor moby pode ser redistribuído quando usado com Azure IoT Edge, e a Microsoft fornece assistência para este motor.

### <a name="choose-upstream-protocol"></a>Escolha o protocolo a montante

Pode configurar o protocolo (que determina a porta utilizada) para comunicação a montante ao IoT Hub tanto para o agente IoT Edge como para o hub IoT Edge. O protocolo predefinido é AMQP, mas pode querer alterá-lo dependendo da configuração da sua rede.

Os dois módulos de tempo de execução têm uma variável ambiente **UpstreamProtocol.** Os valores válidos para a variável são:

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configure a variável UpstreamProtocol para o agente IoT Edge no ficheiro config do próprio dispositivo. Por exemplo, se o seu dispositivo IoT Edge estiver por trás de um servidor proxy que bloqueia as portas AMQP, poderá ter de configurar o agente IoT Edge para utilizar AMQP sobre WebSocket (AMQPWS) para estabelecer a ligação inicial ao IoT Hub.

Uma vez que o seu dispositivo IoT Edge se conecte, certifique-se de que continua a configurar a variável UpstreamProtocol para ambos os módulos de tempo de execução em futuras implementações. Um exemplo deste processo é fornecido em [Configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Implementação

* **Útil**
  * Seja consistente com o protocolo a montante
  * Configurar o armazenamento do anfitrião para módulos de sistema
  * Reduzir o espaço de memória utilizado pelo hub IoT Edge
  * Não utilize versões de depurar imagens de módulos

### <a name="be-consistent-with-upstream-protocol"></a>Seja consistente com o protocolo a montante

Se configurar o agente IoT Edge no seu dispositivo IoT Edge para utilizar um protocolo diferente do AMQP predefinido, então deve declarar o mesmo protocolo em todas as implementações futuras. Por exemplo, se o seu dispositivo IoT Edge estiver por trás de um servidor proxy que bloqueia as portas AMQP, provavelmente configura o dispositivo para ligar sobre AMQP sobre WebSocket (AMQPWS). Quando colocar módulos no dispositivo, configufique o mesmo protocolo AMQPWS para o agente IoT Edge e o hub IoT Edge, ou então o AMQP predefinido irá sobrepor as definições e impedir-lhe de voltar a ligar.

Basta configurar a variável ambiente UpstreamProtocol para os módulos IoT Edge e IoT Edge hub. Quaisquer módulos adicionais adotam qualquer protocolo definido nos módulos de tempo de execução.

Um exemplo deste processo é fornecido em [Configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Configurar o armazenamento do anfitrião para módulos de sistema

O hub e os módulos de agente IoT Edge utilizam o armazenamento local para manter o estado e permitir mensagens entre módulos, dispositivos e a nuvem. Para uma melhor fiabilidade e desempenho, configuure os módulos do sistema para utilizar o armazenamento no sistema de ficheiros hospedeiro.

Para obter mais informações, consulte [o armazenamento do anfitrião para módulos do sistema.](how-to-access-host-storage-from-module.md)

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Reduzir o espaço de memória utilizado pelo hub IoT Edge

Se estiver a implementar dispositivos constrangidos com memória limitada disponível, pode configurar o hub IoT Edge para funcionar numa capacidade mais simplificada e utilizar menos espaço em disco. No entanto, estas configurações limitam o desempenho do hub IoT Edge, por isso encontre o equilíbrio certo que funcione para a sua solução.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Não otimize para o desempenho em dispositivos constrangidos

O hub IoT Edge está otimizado para o desempenho por padrão, por isso tenta alocar grandes pedaços de memória. Esta configuração pode causar problemas de estabilidade em dispositivos menores como o Raspberry Pi. Se estiver a implementar dispositivos com recursos limitados, é melhor definir a variável do ambiente **OptimizeForPerformance** para **falso** no hub IoT Edge.

Quando **o OptimizeForPerformance** é definido como **verdadeiro,** a cabeça do protocolo MQTT utiliza o PooledByteBufferAllocator, que tem um melhor desempenho mas atribui mais memória. O alocador não funciona bem em sistemas operativos de 32 bits ou em dispositivos com pouca memória. Além disso, quando otimizado para o desempenho, o RocksDb atribui mais memória para o seu papel como fornecedor de armazenamento local.

Para obter mais informações, consulte [as questões de estabilidade em dispositivos mais pequenos.](troubleshoot-common-errors.md#stability-issues-on-smaller-devices)

#### <a name="disable-unused-protocols"></a>Desativar protocolos não-desenvolvidos

Outra forma de otimizar o desempenho do hub IoT Edge e reduzir o seu uso de memória é desligar as cabeças de protocolo para quaisquer protocolos que não esteja a usar na sua solução.

As cabeças de protocolo são configuradas definindo variáveis ambientais booleanas para o módulo hub IoT Edge nos seus manifestos de implantação. As três variáveis são:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Todas as três variáveis têm *dois sublinhados* e podem ser definidas como verdadeiras ou falsas.

#### <a name="reduce-storage-time-for-messages"></a>Reduzir o tempo de armazenamento das mensagens

O módulo hub IoT Edge armazena mensagens temporariamente se não puder ser entregue no IoT Hub por qualquer motivo. Pode configurar quanto tempo o hub IoT Edge se mantém em mensagens não entregues antes de as deixar expirar. Se tiver preocupações de memória no seu dispositivo, pode baixar o valor **do timeToLiveSecs** no módulo hub IoT Edge twin.

O valor predefinido do parâmetro timeToLiveSecs é de 7200 segundos, ou seja, duas horas.

### <a name="do-not-use-debug-versions-of-module-images"></a>Não utilize versões de depurar imagens de módulos

Ao passar de cenários de teste para cenários de produção, lembre-se de remover as configurações de depuração dos manifestos de implantação. Verifique se nenhuma das imagens do módulo nos manifestos de implantação tem o sufixo de **\. depuração.** Se adicionar opções para expor portas nos módulos de depurar, remova essas opções de criação também.

## <a name="container-management"></a>Gestão de contentores

* **Importante**
  * Gerir o acesso ao seu registo de contentores
  * Use tags para gerir versões
* **Útil**
  * Guarde os recipientes de tempo de execução no seu registo privado

### <a name="manage-access-to-your-container-registry"></a>Gerir o acesso ao seu registo de contentores

Antes de colocar módulos para a produção de dispositivos IoT Edge, certifique-se de que controla o acesso ao registo do seu contentor para que os forasteiros não possam aceder ou fazer alterações nas imagens do seu contentor. Utilize um registo privado, não público, de contentores para gerir imagens de contentores.

Nos tutoriais e outra documentação, instruímo-lo a utilizar as mesmas credenciais de registo de contentores no seu dispositivo IoT Edge que utiliza na sua máquina de desenvolvimento. Estas instruções destinam-se apenas a ajudá-lo a configurar ambientes de teste e desenvolvimento mais facilmente, e não devem ser seguidos num cenário de produção.

Para um acesso mais seguro ao seu registo, tem uma escolha de opções de [autenticação.](../container-registry/container-registry-authentication.md) Uma autenticação popular e recomendada é usar um diretor de serviço de Ative Directory que seja adequado para aplicações ou serviços para puxar imagens de contentores de uma forma automatizada ou sem supervisão (sem cabeça), como fazem os dispositivos IoT Edge.

Para criar um principal de serviço, execute os dois scripts como descrito na [criação de um principal serviço](../container-registry/container-registry-auth-service-principal.md#create-a-service-principal). Estes scripts fazem as seguintes tarefas:

* O primeiro script cria o principal de serviço. Produz a identificação principal do Serviço e a senha principal do Serviço. Guarde estes valores de forma segura nos seus registos.

* O segundo script cria atribuições de funções para conceder ao diretor de serviço, que pode ser executado posteriormente se necessário. Recomendamos a aplicação da função de utilizador **acrPull** para o `role` parâmetro. Para obter uma lista de funções, consulte [as funções e permissões do Registo do Contentor de Azure](../container-registry/container-registry-roles.md).

Para autenticar usando um principal serviço, forneça o ID principal de serviço e a palavra-passe que obteve a partir do primeiro script. Especifique estas credenciais no manifesto de implantação.

* Para o nome de utilizador ou identificação do cliente, especifique o ID principal do serviço.

* Para obter a palavra-passe ou o segredo do cliente, especifique a senha principal do serviço.

> [!NOTE]
> Depois de implementar uma autenticação de segurança melhorada, desative a definição do **utilizador Admin** para que o nome de utilizador/palavra-passe predefinido já não esteja disponível. No registo do seu contentor no portal Azure, a partir do menu do painel esquerdo em **Definições**, selecione **Teclas de acesso**.

### <a name="use-tags-to-manage-versions"></a>Use tags para gerir versões

Uma etiqueta é um conceito de estivador que você pode usar para distinguir entre versões de recipientes de estivadores. As etiquetas são sufixos como **1.0** que vão na ponta de um repositório de contentores. Por exemplo, **mcr.microsoft.com/azureiotedge-agent:1.0.** As etiquetas são mutáveis e podem ser alteradas para apontar para outro recipiente a qualquer momento, por isso a sua equipa deve concordar com uma convenção a seguir à medida que atualiza as imagens do módulo que avança.

As tags também ajudam a impor atualizações nos seus dispositivos IoT Edge. Quando empurrar uma versão atualizada de um módulo para o registo do seu contentor, incremente a etiqueta. Em seguida, empurre uma nova implementação para os seus dispositivos com a etiqueta incrementada. O motor de contentor reconhecerá a etiqueta incrementada como uma nova versão e puxará a versão mais recente do módulo para o seu dispositivo.

Para um exemplo de uma convenção de tags, consulte [Atualizar o tempo de execução do IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags) para saber como o IoT Edge utiliza tags rolantes e tags específicas para rastrear versões.

### <a name="store-runtime-containers-in-your-private-registry"></a>Guarde os recipientes de tempo de execução no seu registo privado

Sabe guardar as imagens do seu contentor para módulos de código personalizados no seu registo privado de Azure, mas também pode usá-lo para armazenar imagens de contentores públicos, tais como para os módulos de tempo de execução edgeAgent e edgHub. Fazê-lo pode ser necessário se tiver restrições de firewall muito apertadas, uma vez que estes recipientes de tempo de execução são armazenados no Registo de Contentores da Microsoft (MCR).

Obtenha as imagens com o comando de puxar o Docker para colocar no seu registo privado. Esteja ciente de que terá de atualizar as imagens a cada nova versão do tempo de execução do IoT Edge.

| Recipiente de tempo de execução IoT Edge | Estivador puxar comando |
| --- | --- |
| [Agente Azure IoT Edge](https://hub.docker.com/_/microsoft-azureiotedge-agent) | `docker pull mcr.microsoft.com/azureiotedge-agent` |
| [Azure IoT Edge Hub](https://hub.docker.com/_/microsoft-azureiotedge-hub) | `docker pull mcr.microsoft.com/azureiotedge-hub` |

Em seguida, certifique-se de atualizar as referências de imagem no deployment.template.jsem arquivo para os módulos do sistema edgeAgent e edgeHub. Substitua `mcr.microsoft.com` o nome do registo e o servidor por ambos os módulos.

* edgeAgent:

    `"image": "<registry name and server>/azureiotedge-agent:1.1",`

* edgeHub:

    `"image": "<registry name and server>/azureiotedge-hub:1.1",`

## <a name="networking"></a>Rede

* **Útil**
  * Revisão da configuração de saída/entrada
  * Permitir ligações a partir de dispositivos IoT Edge
  * Configurar a comunicação através de um representante

### <a name="review-outboundinbound-configuration"></a>Revisão da configuração de saída/entrada

Os canais de comunicação entre o Azure IoT Hub e o IoT Edge estão sempre configurados para serem de saída. Para a maioria dos cenários IoT Edge, apenas são necessárias três ligações. O motor do contentor precisa de se ligar ao registo do contentor (ou registos) que contém as imagens do módulo. O tempo de funcionaamento do IoT Edge precisa de se conectar com o IoT Hub para recuperar informações de configuração do dispositivo e para enviar mensagens e telemetria. E se utilizar o provisionamento automático, o IoT Edge precisa de se ligar ao Serviço de Provisionamento de Dispositivos. Para obter mais informações, consulte [as regras de configuração de Firewall e porta](troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="allow-connections-from-iot-edge-devices"></a>Permitir ligações a partir de dispositivos IoT Edge

Se a sua configuração de rede exigir que permita explicitamente ligações feitas a partir de dispositivos IoT Edge, reveja a seguinte lista de componentes IoT Edge:

* **O agente IoT Edge** abre uma ligação AMQP/MQTT persistente ao IoT Hub, possivelmente sobre WebSockets.
* **O hub IoT Edge** abre uma única ligação AMQP persistente ou múltiplas ligações MQTT ao IoT Hub, possivelmente sobre WebSockets.
* **O serviço IoT Edge** faz chamadas intermitentes https para o IoT Hub.

Em todos os três casos, o nome DNS corresponderia ao padrão \* .azure-devices.net.

Além disso, o **motor do contentor** faz chamadas para registos de contentores em HTTPS. Para recuperar as imagens do recipiente de tempo de execução IoT Edge, o nome DNS é mcr.microsoft.com. O motor do contentor liga-se a outros registos configurados na implantação.

Esta lista de verificação é um ponto de partida para as regras de firewall:

   | URL \* (= wildcard) | Portas TCP de saída | Utilização |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Registo de contentores da Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Acesso dPS (opcional) |
   | \*.azurecr.io | 443 | Registos pessoais e de contentores de terceiros |
   | \*.blob.core.windows.net | 443 | Descarregue deltas de imagem do registo de contentores Azure do armazenamento de bolhas |
   | \*.azure-devices.net | 5671, 8883, 443 | Acesso ao Hub IoT |
   | \*.docker.io  | 443 | Acesso ao Docker Hub (opcional) |

Algumas destas regras de firewall são herdadas do Registo de Contentores de Azure. Para obter mais informações, consulte [as regras de configuração para aceder a um registo de contentores Azure atrás de uma firewall](../container-registry/container-registry-firewall-access-rules.md).

> [!NOTE]
> Para fornecer uma FQDN consistente entre o REST e os pontos finais de dados, a partir **de 15 de junho de 2020,** o ponto final de dados do Registo de Contentores da Microsoft mudará de `*.cdn.mscr.io` para `*.data.mcr.microsoft.com`  
> Para obter mais informações, consulte a [configuração das regras de firewall do cliente do Microsoft Container Registry](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)

Se não quiser configurar a sua firewall para permitir o acesso aos registos de contentores públicos, pode armazenar imagens no seu registo privado de contentores, conforme descrito nos recipientes de tempo de [funcionamento da Loja no seu registo privado.](#store-runtime-containers-in-your-private-registry)

### <a name="configure-communication-through-a-proxy"></a>Configurar a comunicação através de um representante

Se os seus dispositivos forem implantados numa rede que utilize um servidor proxy, eles precisam de ser capazes de comunicar através do proxy para chegar ao IoT Hub e registos de contentores. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Gestão de soluções

* **Útil**
  * Configurar registos e diagnósticos
  * Considere os testes e os gasodutos CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Configurar registos e diagnósticos

No Linux, o daemon IoT Edge utiliza diários como controlador de registo padrão. Pode utilizar a ferramenta de linha de comando `journalctl` para consultar os registos daemon.

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

Começando pela versão 1.2, o IoT Edge conta com vários daemons. Embora os registos de cada daemon possam ser consultados `journalctl` individualmente, os `iotedge system` comandos fornecem uma maneira conveniente de consultar os registos combinados.

* Comando `iotedge` consolidado:

  ```bash
  sudo iotedge system logs
  ```

* Comando `journalctl` equivalente:

  ```bash
  journalctl -u aziot-edge -u aziot-identityd -u aziot-keyd -u aziot-certd -u aziot-tpmd
  ```

:::moniker-end

No Windows, o daemon IoT Edge utiliza diagnósticos PowerShell. Use `Get-IoTEdgeLog` para consultar registos do daemon. Os módulos IoT Edge utilizam o controlador JSON para fazer login, que é o padrão.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Quando está a testar uma implementação IoT Edge, normalmente pode aceder aos seus dispositivos para recuperar registos e resolução de problemas. Num cenário de implantação, pode não ter essa opção. Considere como vai recolher informações sobre os seus dispositivos em produção. Uma opção é utilizar um módulo de registo que recolhe informações dos outros módulos e a envia para a nuvem. Um exemplo de um módulo de registo é [logspout-loganalytics,](https://github.com/veyalla/logspout-loganalytics)ou pode desenhar o seu próprio.

### <a name="place-limits-on-log-size"></a>Coloque limites no tamanho do tronco

Por predefinição, o motor do contentor Moby não estabelece limites de tamanho do registo de contentores. Com o tempo isto pode levar a que o dispositivo se encha de registos e fique sem espaço em disco. Considere as seguintes opções para evitar isto:

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>Opção: Definir limites globais aplicáveis a todos os módulos de contentores

Pode limitar o tamanho de todos os registos de contentores nas opções de registo do motor do recipiente. O exemplo a seguir define o controlador de registo para `json-file` (recomendado) com limites no tamanho e número de ficheiros:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Adicione (ou apêndice) esta informação a um ficheiro nomeado `daemon.json` e coloque-a no local certo para a plataforma do seu dispositivo.

| Plataforma | Localização |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

O motor do recipiente deve ser reiniciado para que as alterações entrem em vigor.

#### <a name="option-adjust-log-settings-for-each-container-module"></a>Opção: Ajustar as definições de registo para cada módulo de contentor

Pode fazê-lo nas **opções de criação** de cada módulo. Por exemplo:

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

#### <a name="additional-options-on-linux-systems"></a>Opções adicionais nos sistemas Linux

* Configure o motor do recipiente para enviar registos para `systemd` [o diário](https://docs.docker.com/config/containers/logging/journald/) definindo `journald` como o controlador de registo predefinido.

* Retire periodicamente os troncos antigos do seu dispositivo instalando uma ferramenta logrotate. Utilize a seguinte especificação de ficheiro:

   ```txt
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Considere os testes e os gasodutos CI/CD

Para o cenário de implementação IoT Edge mais eficiente, considere integrar a sua implantação de produção nos seus testes e oleodutos CI/CD. Azure IoT Edge suporta várias plataformas ci/CD, incluindo Azure DevOps. Para obter mais informações, consulte [integração contínua e implementação contínua para Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a implementação automática IoT Edge](module-deployment-monitoring.md).
* Veja como o IoT Edge suporta [a integração contínua e a implementação contínua.](how-to-continuous-integration-continuous-deployment.md)
