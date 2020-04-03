---
title: Prepare-se para implementar a sua solução em produção - Azure IoT Edge
description: Saiba como levar a sua solução Azure IoT Edge do desenvolvimento à produção, incluindo a configuração dos seus dispositivos com os certificados apropriados e a elaboração de um plano de implementação para futuras atualizações de código.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: dd24631f8e6b4f3f87438bf22654016dd7699950
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618309"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Prepare-se para implementar a sua solução IoT Edge em produção

Quando estiver pronto para levar a sua solução IoT Edge do desenvolvimento para a produção, certifique-se de que está configurada para um desempenho contínuo.

A informação fornecida neste artigo não é igual. Para ajudá-lo a priorizar, cada secção começa com listas que dividem o trabalho em duas secções: **importante** para completar antes de ir à produção, ou **útil** para que você saiba.

## <a name="device-configuration"></a>Configuração do dispositivo

Os dispositivos IoT Edge podem ser qualquer coisa, desde um Raspberry Pi até um portátil até uma máquina virtual a funcionar num servidor. Pode ter acesso ao dispositivo fisicamente ou através de uma ligação virtual, ou pode ficar isolado por longos períodos de tempo. De qualquer forma, você quer ter certeza de que está configurado para funcionar apropriadamente.

* **Importante**
  * Instalar certificados de produção
  * Ter um plano de gestão de dispositivos
  * Use Moby como motor de contentor

* **Útil**
  * Escolha o protocolo a montante

### <a name="install-production-certificates"></a>Instalar certificados de produção

Todos os dispositivos IoT Edge em produção precisam de um certificado de autoridade de certificados de dispositivo (CA) instalado nele. O certificado ca é declarado ao tempo de execução da Borda IoT no ficheiro config.yaml. Para cenários de desenvolvimento e teste, o tempo de execução do IoT Edge cria certificados temporários se não forem declarados certificados no ficheiro config.yaml. No entanto, estes certificados temporários expiram após três meses e não são seguros para cenários de produção.

Para compreender o papel do certificado CA do dispositivo, veja [como o Azure IoT Edge utiliza certificados](iot-edge-certs.md).

Para obter mais informações sobre como instalar certificados num dispositivo IoT Edge e remeti-los a partir do ficheiro config.yaml, consulte Instalar certificados de [produção num dispositivo IoT Edge](how-to-manage-device-certificates.md).

### <a name="have-a-device-management-plan"></a>Ter um plano de gestão de dispositivos

Antes de colocar qualquer dispositivo em produção, deve saber como vai gerir futuras atualizações. Para um dispositivo IoT Edge, a lista de componentes a atualizar pode incluir:

* Firmware do dispositivo
* Bibliotecas do sistema operativo
* Motor de contentor, como Moby
* IoT Edge daemon
* Certificados da AC

Para mais informações, consulte Atualizar o tempo de [execução do IoT Edge](how-to-update-iot-edge.md). Os métodos atuais para atualizar o daemon IoT Edge requerem acesso físico ou SSH ao dispositivo IoT Edge. Se tiver muitos dispositivos para atualizar, considere adicionar os passos de atualização a um script ou use uma ferramenta de automação como o Ansible.

### <a name="use-moby-as-the-container-engine"></a>Use Moby como motor de contentor

Um motor de contentor é um pré-requisito para qualquer dispositivo IoT Edge. Apenas moby-engine é suportado na produção. Outros motores de contentores, como o Docker, trabalham com o IoT Edge e não faz mal usar estes motores para o desenvolvimento. O moby-engine pode ser redistribuído quando utilizado com O Edge Azure IoT, e a Microsoft fornece manutenção para este motor.

### <a name="choose-upstream-protocol"></a>Escolha o protocolo a montante

Pode configurar o protocolo (que determina a porta utilizada) para a comunicação a montante ao IoT Hub tanto para o agente IoT Edge como para o hub IoT Edge. O protocolo padrão é AMQP, mas pode querer alterá-lo dependendo da configuração da sua rede.

Os dois módulos de tempo de execução têm uma variável ambiente **UpstreamProtocol.** Os valores válidos para a variável são:

* MQTT
* AMQP
* Mqttws
* AMQPWS

Configure a variável UpstreamProtocol para o agente IoT Edge no ficheiro config.yaml no próprio dispositivo. Por exemplo, se o seu dispositivo IoT Edge estiver por trás de um servidor proxy que bloqueie as portas AMQP, poderá ser necessário configurar o agente IoT Edge para utilizar amQP sobre WebSocket (AMQPWS) para estabelecer a ligação inicial ao IoT Hub.

Assim que o seu dispositivo IoT Edge se ligar, certifique-se de que continua a configurar a variável UpstreamProtocol para ambos os módulos de tempo de execução em futuras implementações. Um exemplo deste processo é fornecido no [Configure um dispositivo IoT Edge para comunicar através](how-to-configure-proxy-support.md)de um servidor proxy .

## <a name="deployment"></a>Implementação

* **Útil**
  * Seja consistente com o protocolo a montante
  * Configurar o armazenamento do hospedeiro para módulos de sistema
  * Reduza o espaço de memória utilizado pelo hub IoT Edge
  * Não utilize versões de depuração de imagens de módulos

### <a name="be-consistent-with-upstream-protocol"></a>Seja consistente com o protocolo a montante

Se configurar o agente IoT Edge no seu dispositivo IoT Edge para utilizar um protocolo diferente do AMQP padrão, então deverá declarar o mesmo protocolo em todas as futuras implementações. Por exemplo, se o seu dispositivo IoT Edge estiver por trás de um servidor proxy que bloqueia as portas AMQP, provavelmente configurao o dispositivo para se ligar sobre AMQP sobre WebSocket (AMQPWS). Quando implementar módulos para o dispositivo, configure o mesmo protocolo AMQPWS para o agente IoT Edge e o hub IoT Edge, ou então o AMQP padrão irá sobrepor-se às definições e impedir que se conecte novamente.

Basta configurar a variável ambiente UpstreamProtocol para o agente IoT Edge e módulos de hub IoT Edge. Quaisquer módulos adicionais adotem qualquer protocolo definido nos módulos de tempo de execução.

Um exemplo deste processo é fornecido no [Configure um dispositivo IoT Edge para comunicar através](how-to-configure-proxy-support.md)de um servidor proxy .

### <a name="set-up-host-storage-for-system-modules"></a>Configurar o armazenamento do hospedeiro para módulos de sistema

Os módulos do hub e do agente IoT Edge utilizam o armazenamento local para manter o estado e permitir a mensagem entre módulos, dispositivos e a nuvem. Para uma melhor fiabilidade e desempenho, configure os módulos do sistema para utilizar o armazenamento no sistema de ficheiros anfitrião.

Para mais informações, consulte [o armazenamento do anfitrião para os módulos do sistema](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Reduzir o espaço de memória usado pelo hub IoT Edge

Se estiver a implementar dispositivos constrangidos com memória limitada disponível, pode configurar o hub IoT Edge para funcionar numa capacidade mais simplificada e utilizar menos espaço em disco. Estas configurações limitam o desempenho do hub IoT Edge, no entanto, encontre o equilíbrio certo que funciona para a sua solução.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Não otimize o desempenho em dispositivos constrangidos

O hub IoT Edge está otimizado para o desempenho por padrão, pelo que tenta alocar grandes pedaços de memória. Esta configuração pode causar problemas de estabilidade em dispositivos mais pequenos como o Raspberry Pi. Se estiver a implementar dispositivos com recursos limitados, poderá querer definir a variável ambiente **OptimizeForPerformance** para **falsa** no hub IoT Edge.

Quando o **OptimizeForPerformance** está definido para **ser verdade,** o chefe do protocolo MQTT utiliza o Localizador PooledByteBufferAl, que tem melhor desempenho mas aloca mais memória. O alocósero não funciona bem em sistemas operativos de 32 bits ou em dispositivos com pouca memória. Além disso, quando otimizado para o desempenho, o RocksDb aloca mais memória pelo seu papel como fornecedor de armazenamento local.

Para obter mais informações, consulte [questões de estabilidade em dispositivos limitados](troubleshoot.md#stability-issues-on-resource-constrained-devices)de recursos .

#### <a name="disable-unused-protocols"></a>Desativar protocolos não utilizados

Outra forma de otimizar o desempenho do hub IoT Edge e reduzir o seu uso de memória é desligar as cabeças do protocolo para quaisquer protocolos que não esteja a usar na sua solução.

As cabeças protocolares são configuradas através da definição de variáveis ambientais booleanas para o módulo hub IoT Edge nos seus manifestos de implantação. As três variáveis são:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Todas as três variáveis têm *dois sublinhados* e podem ser definidas para verdade ou falso.

#### <a name="reduce-storage-time-for-messages"></a>Reduzir o tempo de armazenamento das mensagens

O módulo ioT Edge armazena mensagens temporariamente se não puderem ser entregues no IoT Hub por qualquer motivo. Pode configurar quanto tempo o hub IoT Edge se mantém em mensagens não entregues antes de as deixar expirar. Se tiver preocupações de memória no seu dispositivo, pode baixar o valor do **tempoToLiveSecs** no módulo hub IoT Edge twin.

O valor padrão do parâmetro timeToLiveSecs é de 7200 segundos, o que é de duas horas.

### <a name="do-not-use-debug-versions-of-module-images"></a>Não utilize versões de depuração de imagens de módulos

Ao passar dos cenários de teste para os cenários de produção, lembre-se de remover as configurações de depuração dos manifestos de implantação. Verifique se nenhuma das imagens do módulo nos manifestos de implantação tem o sufixo ** \.de depuração.** Se adicionou opções para expor portas nos módulos para depuração, remova-as também.

## <a name="container-management"></a>Gestão de contentores

* **Importante**
  * Gerencie o acesso ao seu registo de contentores
  * Use tags para gerir versões

### <a name="manage-access-to-your-container-registry-with-a-service-principal"></a>Gerencie o acesso ao seu registo de contentores com um diretor de serviço

Antes de implementar módulos para produzir dispositivos IoT Edge, certifique-se de que controla o acesso ao registo do seu contentor para que os forasteiros não possam aceder ou fazer alterações nas imagens do seu contentor. Utilize um registo privado, não público, de contentores para gerir imagens de contentores.

Nos tutoriais e outradocumentação, instruímos-o a usar as mesmas credenciais de registo de contentores no seu dispositivo IoT Edge que utiliza na sua máquina de desenvolvimento. Estas instruções destinam-se apenas a ajudá-lo a configurar ambientes de teste e desenvolvimento mais facilmente, e não deve ser seguido num cenário de produção. O Registo de Contentores Azure recomenda [a autenticação com os diretores](../container-registry/container-registry-auth-service-principal.md) de serviço quando aplicações ou serviços puxam imagens de contentores de forma automatizada ou sem vigilância (sem cabeça), como fazem os dispositivos IoT Edge.

Para criar um diretor de serviço, execute os dois scripts conforme descrito na [criação de um diretor](../container-registry/container-registry-auth-aci.md#create-a-service-principal)de serviço . Estes scripts fazem as seguintes tarefas:

* O primeiro guião cria o diretor de serviço. Produz o ID principal do Serviço e a senha principal do Serviço. Guarde estes valores de forma segura nos seus registos.

* O segundo guião cria atribuições de funções para conceder ao diretor de serviço, que pode ser executado posteriormente, se necessário. Recomendamos a aplicação da função `role` de utilizador **acrPull** para o parâmetro. Para obter uma lista de funções, consulte [funções e permissões](../container-registry/container-registry-roles.md) do Registo de Contentores do Azure

Para autenticar utilizando um diretor de serviço, forneça o ID principal de serviço e a palavra-passe que obteve a partir do primeiro script.

* Para o nome de utilizador ou identificação do cliente, especifique o ID principal do serviço.

* Para a palavra-passe ou segredo do cliente, especifique a palavra-passe principal do serviço.

Para um exemplo de lançamento de um contentor com o Azure CLI, consulte [Authenticate utilizando o diretor de serviço](../container-registry/container-registry-auth-aci.md#authenticate-using-the-service-principal).

### <a name="use-tags-to-manage-versions"></a>Use tags para gerir versões

Uma etiqueta é um conceito de estivador que pode usar para distinguir entre versões de recipientes de estivadores. As etiquetas são sufixos como **1.0** que vão na extremidade de um repositório de contentores. Por exemplo, **mcr.microsoft.com/azureiotedge-agent:1.0.** As etiquetas são mutáveis e podem ser alteradas para apontar para outro recipiente a qualquer momento, pelo que a sua equipa deve concordar com uma convenção a seguir à medida que atualiza as imagens do módulo.

As tags também o ajudam a impor atualizações nos seus dispositivos IoT Edge. Quando empurrar uma versão atualizada de um módulo para o seu registo de contentores, incremente a etiqueta. Em seguida, empurre uma nova implementação para os seus dispositivos com a etiqueta incrementada. O motor do recipiente reconhecerá a etiqueta incrementada como uma nova versão e puxará a versão mais recente do módulo para o seu dispositivo.

Para um exemplo de uma convenção de etiquetas, consulte Atualizar o tempo de [funcionação do IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags) para saber como o IoT Edge utiliza tags rolantes e etiquetas específicas para rastrear versões.

## <a name="networking"></a>Redes

* **Útil**
  * Rever a configuração de saída/entrada
  * Permitir ligações a partir de dispositivos IoT Edge
  * Configure a comunicação através de um proxy

### <a name="review-outboundinbound-configuration"></a>Rever a configuração de saída/entrada

Os canais de comunicação entre o Hub Azure IoT e o IoT Edge estão sempre configurados para estarem de saída. Para a maioria dos cenários de IoT Edge, apenas três ligações são necessárias. O motor do recipiente tem de se ligar ao registo do contentor (ou registos) que detém as imagens do módulo. O tempo de execução do IoT Edge precisa de se ligar ao IoT Hub para recuperar informações de configuração do dispositivo e enviar mensagens e telemetria. E se utilizar o fornecimento automático, o daemon IoT Edge precisa de se ligar ao Serviço de Provisionamento de Dispositivos. Para mais informações, consulte [as regras de configuração da Firewall e da porta](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>Permitir ligações a partir de dispositivos IoT Edge

Se a sua configuração de rede exigir que permita explicitamente ligações feitas a partir de dispositivos IoT Edge, reveja a seguinte lista de componentes IoT Edge:

* **O agente IoT Edge** abre uma ligação AMQP/MQTT persistente ao IoT Hub, possivelmente por WebSockets.
* **O hub IoT Edge** abre uma única ligação AMQP persistente ou múltiplas ligações MQTT ao IoT Hub, possivelmente por WebSockets.
* **IoT Edge daemon** faz chamadas intermitentes HTTPS para IoT Hub.

Em todos os três casos, o \*nome DNS corresponderia ao padrão .azure-devices.net.

Além disso, o **motor Container** faz chamadas para registos de contentores em HTTPS. Para recuperar as imagens do recipiente de tempo de execução IoT Edge, o nome DNS é mcr.microsoft.com. O motor do contentor liga-se a outros registos, tal como configurado na implantação.

Esta lista de verificação é um ponto de partida para as regras de firewall:

   | URL\* ( = wildcard) | Portas TCP de saída | Utilização |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Registo de contentores da Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Acesso dPS (opcional) |
   | \*.azurecr.io | 443 | Registos pessoais e de terceiros |
   | \*.blob.core.windows.net | 443 | Baixe a imagem do Registo de Contentores Azure deltas do armazenamento de blob |
   | \*.azure-devices.net | 5671, 8883, 443 | Acesso ao Hub IoT |
   | \*.docker.io  | 443 | Acesso ao Docker Hub (opcional) |

Algumas destas regras de firewall são herdadas do Registo de Contentores De Azure. Para mais informações, consulte [as regras do Configure para aceder a um registo de contentores Azure atrás de uma firewall](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>Configure a comunicação através de um proxy

Se os seus dispositivos vão ser implantados numa rede que utiliza um servidor proxy, precisam de ser capazes de comunicar através do proxy para alcançar o IoT Hub e registos de contentores. Para mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Gestão de soluções

* **Útil**
  * Configurar registos e diagnósticos
  * Considere os testes e os oleodutos CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Configurar registos e diagnósticos

Em Linux, o daemon IoT Edge usa os diários como o condutor de registo padrão. Pode utilizar a ferramenta `journalctl` de linha de comando para consultar os registos daemon. No Windows, o daemon IoT Edge utiliza diagnósticos PowerShell. Utilize `Get-IoTEdgeLog` para consultar os registos do daemon. Os módulos IoT Edge utilizam o controlador JSON para a exploração madeireira, que é a falha.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Quando está a testar uma implementação ioT Edge, normalmente pode aceder aos seus dispositivos para recuperar registos e problemas. Num cenário de implantação, pode não ter essa opção. Considere como vai recolher informações sobre os seus dispositivos em produção. Uma opção é usar um módulo de registo que recolhe informações dos outros módulos e envia-as para a nuvem. Um exemplo de um módulo de registo é [logspout-loganalytics,](https://github.com/veyalla/logspout-loganalytics)ou pode projetar o seu próprio.

### <a name="place-limits-on-log-size"></a>Coloque limites no tamanho do tronco

Por defeito, o motor do contentor Moby não define os limites de tamanho do registo do recipiente. Com o tempo isto pode levar ao enchimento do dispositivo com troncos e a ficar sem espaço de disco. Considere as seguintes opções para evitar isto:

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>Opção: Definir limites globais que se aplicam a todos os módulos de contentores

Pode limitar o tamanho de todos os ficheiros de registo de contentores nas opções de registo do motor do recipiente. O exemplo seguinte define o `json-file` controlador de registo para (recomendado) com limites de tamanho e número de ficheiros:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Adicione (ou anexar) estas informações a um ficheiro nomeado `daemon.json` e coloque-as no local certo para a sua plataforma de dispositivos.

| Plataforma | Localização |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

O motor do recipiente deve ser reiniciado para que as alterações entrem em vigor.

#### <a name="option-adjust-log-settings-for-each-container-module"></a>Opção: Ajuste as definições de registo para cada módulo de recipiente

Pode fazê-lo na **criação De Opções** de cada módulo. Por exemplo:

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

* Configure o motor do recipiente `systemd` para `journald` enviar registos para [o diário,](https://docs.docker.com/config/containers/logging/journald/) definindo como controlador de registo predefinido.

* Remova periodicamente os registos antigos do seu dispositivo instalando uma ferramenta de logrotate. Utilize a seguinte especificação de ficheiro:

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

### <a name="consider-tests-and-cicd-pipelines"></a>Considere os testes e os oleodutos CI/CD

Para o cenário de implantação mais eficiente do IoT Edge, considere integrar a sua implantação de produção nos seus testes e oleodutos CI/CD. O Azure IoT Edge suporta várias plataformas CI/CD, incluindo O Azure DevOps. Para mais informações, consulte a [integração contínua e a implantação contínua para o Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a [implementação automática IoT Edge](module-deployment-monitoring.md).
* Veja como o IoT Edge suporta a [integração contínua e a implantação contínua.](how-to-ci-cd.md)
