---
title: Preparar dispositivos e implantações para produção – Azure IoT Edge | Microsoft Docs
description: Saiba como levar sua solução de Azure IoT Edge de desenvolvimento para produção, incluindo a configuração de seus dispositivos com os certificados apropriados e como fazer um plano de implantação para futuras atualizações de código.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 599b5b075f32294f9e68c776c4a7744283e9c269
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244049"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Prepare-se para implantar sua solução de IoT Edge em produção

Quando estiver pronto para levar sua solução de IoT Edge do desenvolvimento para a produção, verifique se ela está configurada para desempenho contínuo.

As informações fornecidas neste artigo não são iguais. Para ajudá-lo a priorizar, cada seção começa com listas que dividem o trabalho em duas seções: **importante** para concluir antes de passar para a produção ou **útil** para você saber.

## <a name="device-configuration"></a>Configuração do dispositivo

IoT Edge dispositivos pode ser qualquer coisa, desde um Raspberry Pi até um laptop e uma máquina virtual em execução em um servidor. Você pode ter acesso ao dispositivo fisicamente ou por meio de uma conexão virtual, ou pode ser isolado por longos períodos de tempo. De qualquer forma, você quer ter certeza de que ele está configurado para funcionar adequadamente. 

* **Importante**
    * Instalar certificados de produção
    * Ter um plano de gerenciamento de dispositivos
    * Usar Moby como o mecanismo de contêiner

* **Complementos**
    * Escolher protocolo upstream

### <a name="install-production-certificates"></a>Instalar certificados de produção

Cada dispositivo de IoT Edge em produção precisa de um certificado de autoridade de certificação de dispositivo instalado nele. Esse certificado de autoridade de certificação é então declarado para o tempo de execução de IoT Edge no arquivo config. YAML. Para facilitar o desenvolvimento e o teste, o tempo de execução do IoT Edge cria certificados temporários se nenhum certificado for declarado no arquivo config. YAML. No entanto, esses certificados temporários expiram após três meses e não são seguros para cenários de produção. 

Para entender a função do certificado de autoridade de certificação do dispositivo, consulte [como Azure IOT Edge usa certificados](iot-edge-certs.md).

Para obter mais informações sobre como instalar certificados em um dispositivo IoT Edge e referenciá-los do arquivo config. YAML, consulte [configurar um dispositivo de IOT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md). As etapas para configurar os certificados são as mesmas se o dispositivo será usado como um gateway ou não. Esse artigo fornece scripts para gerar certificados de exemplo apenas para teste. Não use esses certificados de exemplo em produção. 

### <a name="have-a-device-management-plan"></a>Ter um plano de gerenciamento de dispositivos

Antes de colocar qualquer dispositivo em produção, você deve saber como vai gerenciar atualizações futuras. Para um dispositivo IoT Edge, a lista de componentes a serem atualizados pode incluir:

* Firmware do dispositivo
* Bibliotecas do sistema operacional
* Mecanismo de contêiner, como Moby
* Daemon de IoT Edge
* Certificados de AC

Para obter mais informações, consulte [atualizar o tempo de execução de IOT Edge](how-to-update-iot-edge.md). Os métodos atuais para atualizar o daemon de IoT Edge exigem acesso físico ou SSH ao dispositivo IoT Edge. Se você tiver muitos dispositivos para atualizar, considere adicionar as etapas de atualização a um script ou usar uma ferramenta de automação como Ansible.

### <a name="use-moby-as-the-container-engine"></a>Usar Moby como o mecanismo de contêiner

Um mecanismo de contêiner é um pré-requisito para qualquer dispositivo de IoT Edge. Só há suporte para Moby no mecanismo de produção. Outros mecanismos de contêiner, como o Docker, funcionam com IoT Edge e estão OK para usar esses mecanismos para desenvolvimento. O Moby-Engine pode ser redistribuído quando usado com Azure IoT Edge e a Microsoft fornece serviços para esse mecanismo.

### <a name="choose-upstream-protocol"></a>Escolher protocolo upstream

O protocolo (e, portanto, a porta usada) para comunicação upstream com o Hub IoT pode ser configurado para o agente de IoT Edge e o Hub de IoT Edge. O protocolo padrão é AMQP, mas talvez você queira alterar isso dependendo de sua configuração de rede. 

Os dois módulos de tempo de execução têm uma variável de ambiente **UpstreamProtocol** . Os valores válidos para a variável são: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configure a variável UpstreamProtocol para o agente de IoT Edge no arquivo config. YAML no próprio dispositivo. Por exemplo, se o dispositivo de IoT Edge estiver protegido por um servidor proxy que bloqueia as portas AMQP, talvez seja necessário configurar o agente de IoT Edge para usar o AMQP sobre WebSocket (AMQPWS) para estabelecer a conexão inicial com o Hub IoT. 

Depois que o dispositivo IoT Edge se conectar, certifique-se de continuar Configurando a variável UpstreamProtocol para ambos os módulos de tempo de execução em implantações futuras. Um exemplo desse processo é fornecido em [configurar um dispositivo de IOT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Implementação

* **Complementos**
    * Ser consistente com o protocolo upstream
    * Configurar o armazenamento de host para módulos do sistema
    * Reduzir o espaço de memória usado pelo hub de IoT Edge
    * Não usar versões de depuração de imagens de módulo

### <a name="be-consistent-with-upstream-protocol"></a>Ser consistente com o protocolo upstream

Se você tiver configurado o agente de IoT Edge em seu dispositivo de IoT Edge para usar um protocolo diferente do AMQP padrão, deverá declarar o mesmo protocolo em todas as implantações futuras. Por exemplo, se o dispositivo de IoT Edge estiver protegido por um servidor proxy que bloqueia as portas AMQP, você provavelmente configurou o dispositivo para se conectar por meio do AMQP sobre WebSocket (AMQPWS). Ao implantar módulos no dispositivo, configure o mesmo protocolo AMQPWS para o agente de IoT Edge e IoT Edge Hub ou, caso contrário, o AMQP padrão substituirá as configurações e impedirá que você se conecte novamente. 

Você só precisa configurar a variável de ambiente UpstreamProtocol para os módulos agente de IoT Edge e IoT Edge Hub. Quaisquer módulos adicionais adotam qualquer protocolo definido nos módulos de tempo de execução. 

Um exemplo desse processo é fornecido em [configurar um dispositivo de IOT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Configurar o armazenamento de host para módulos do sistema

O Hub IoT Edge e os módulos de agente usam o armazenamento local para manter o estado e habilitar o sistema de mensagens entre módulos, dispositivos e a nuvem. Para melhorar a confiabilidade e o desempenho, configure os módulos do sistema para usar o armazenamento no sistema de arquivos do host.

Para obter mais informações, consulte [host Storage for System modules](offline-capabilities.md#host-storage-for-system-modules).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Reduzir o espaço de memória usado pelo hub de IoT Edge

Se estiver implantando dispositivos restritos com memória limitada disponível, você poderá configurar IoT Edge Hub para ser executado em uma capacidade mais simplificada e usar menos espaço em disco. No entanto, essas configurações limitam o desempenho do hub de IoT Edge, portanto, encontre o equilíbrio certo que funciona para sua solução. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Não otimizar o desempenho em dispositivos restritos

O Hub de IoT Edge é otimizado para desempenho por padrão, portanto, ele tenta alocar grandes partes de memória. Essa configuração pode causar problemas de estabilidade em dispositivos menores, como o Raspberry Pi. Se você estiver implantando dispositivos com recursos restritos, talvez queira definir a variável de ambiente **OptimizeForPerformance** como **false** no Hub de IOT Edge. 

Para obter mais informações, consulte [problemas de estabilidade em dispositivos com restrição de recursos](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Desabilitar protocolos não utilizados

Outra maneira de otimizar o desempenho do hub de IoT Edge e reduzir seu uso de memória é desativar os cabeçotes de protocolo para quaisquer protocolos que você não esteja usando em sua solução. 

Os cabeçotes de protocolo são configurados Definindo variáveis de ambiente booliano para o módulo IoT Edge Hub em seus manifestos de implantação. As três variáveis são:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Todas as três variáveis têm *dois sublinhados* e podem ser definidas como true ou false. 

#### <a name="reduce-storage-time-for-messages"></a>Reduzir o tempo de armazenamento para mensagens

O módulo Hub de IoT Edge armazenará mensagens temporariamente se não puderem ser entregues ao Hub IoT por qualquer motivo. Você pode configurar quanto tempo o Hub de IoT Edge mantém em mensagens não entregues antes de deixá-las expirarem. Se você tiver problemas de memória em seu dispositivo, poderá diminuir o valor de **timeToLiveSecs** no módulo de hub de IOT Edge e... 

O valor padrão do parâmetro timeToLiveSecs é 7200 segundos, que é de duas horas. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Não usar versões de depuração de imagens de módulo

Ao mover de cenários de teste para cenários de produção, lembre-se de remover configurações de depuração de manifestos de implantação. Verifique se nenhuma das imagens de módulo nos manifestos de implantação tem o sufixo **\.debug** . Se você adicionou opções de criação para expor portas nos módulos para depuração, remova as opções de criação também. 

## <a name="container-management"></a>Gestão de contentores

* **Importante**
    * Gerenciar o acesso ao seu registro de contêiner
    * Usar marcas para gerenciar versões

### <a name="manage-access-to-your-container-registry"></a>Gerenciar o acesso ao seu registro de contêiner

Antes de implantar módulos em dispositivos de IoT Edge de produção, certifique-se de controlar o acesso ao registro de contêiner para que os insideres não possam acessar ou fazer alterações nas imagens de contêiner. Use um registro de contêiner privado e não público para gerenciar imagens de contêiner. 

Nos tutoriais e em outras documentações, instruímos você a usar as mesmas credenciais de registro de contêiner em seu dispositivo IoT Edge como você usa em seu computador de desenvolvimento. Essas instruções são destinadas apenas para ajudá-lo a configurar ambientes de teste e desenvolvimento com mais facilidade e não devem ser seguidos em um cenário de produção. O registro de contêiner do Azure recomenda [a autenticação com entidades de serviço](../container-registry/container-registry-auth-service-principal.md) quando aplicativos ou serviços efetuam pull de imagens de contêiner de maneira automatizada ou autônoma, como IOT Edge dispositivos fazem. Crie uma entidade de serviço com acesso somente leitura ao seu registro de contêiner e forneça esse nome de usuário e senha no manifesto de implantação.

### <a name="use-tags-to-manage-versions"></a>Usar marcas para gerenciar versões

Uma marca é um conceito de Docker que você pode usar para distinguir entre versões de contêineres do Docker. Marcas são sufixos como **1,0** que vão para o final de um repositório de contêiner. Por exemplo, **MCR.Microsoft.com/azureiotedge-Agent:1.0**. As marcas são mutáveis e podem ser alteradas para apontar para outro contêiner a qualquer momento, de modo que sua equipe deve concordar com uma convenção a seguir à medida que você atualiza as imagens do módulo avançando. 

As marcas também ajudam a impor atualizações em seus dispositivos IoT Edge. Quando você envia por push uma versão atualizada de um módulo para o registro de contêiner, incrementa a marca. Em seguida, envie uma nova implantação para seus dispositivos com a marca incrementada. O mecanismo de contêiner reconhecerá a marca incrementada como uma nova versão e obterá a versão mais recente do módulo para o dispositivo. 

Para obter um exemplo de uma Convenção de marca, consulte [atualizar o IOT Edge Runtime](how-to-update-iot-edge.md#understand-iot-edge-tags) para saber como IOT Edge usa marcas sem interrupção e marcas específicas para controlar versões. 

## <a name="networking"></a>Funcionamento em Rede

* **Complementos**
    * Examinar a configuração de saída/entrada
    * Permitir conexões de dispositivos IoT Edge
    * Configurar a comunicação por meio de um proxy

### <a name="review-outboundinbound-configuration"></a>Examinar a configuração de saída/entrada

Os canais de comunicação entre o Hub IoT do Azure e IoT Edge são sempre configurados para serem de saída. Para a maioria dos cenários de IoT Edge, apenas três conexões são necessárias. O mecanismo de contêiner precisa se conectar com o registro de contêiner (ou registros) que contém as imagens de módulo. O tempo de execução do IoT Edge precisa se conectar ao Hub IoT para recuperar informações de configuração do dispositivo e para enviar mensagens e telemetria. E se você usar o provisionamento automático, o daemon de IoT Edge precisará se conectar ao serviço de provisionamento de dispositivos. Para obter mais informações, consulte [regras de configuração de firewall e porta](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>Permitir conexões de dispositivos IoT Edge

Se sua configuração de rede exigir que você explicitamente permita conexões feitas de dispositivos IoT Edge, examine a seguinte lista de componentes de IoT Edge:

* **IOT Edge Agent** abre uma conexão persistente de AMQP/MQTT com o Hub IOT, possivelmente por WebSockets. 
* **IOT Edge Hub** abre uma única conexão AMQP persistente ou várias conexões MQTT para o Hub IOT, possivelmente por WebSockets. 
* **IOT Edge daemon** faz chamadas http intermitentes para o Hub IOT. 

Em todos os três casos, o nome DNS corresponderia ao padrão \*.azure-devices.net. 

Além disso, o **mecanismo de contêiner** faz chamadas para registros de contêiner por HTTPS. Para recuperar as imagens de contêiner do IoT Edge Runtime, o nome DNS é mcr.microsoft.com. O mecanismo de contêiner se conecta a outros registros conforme configurado na implantação. 

Esta lista de verificação é um ponto de partida para regras de firewall:

   | URL (\* = curinga) | Portas TCP de saída | Utilização |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Registro de contêiner da Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Acesso ao DPS (opcional) |
   | \*.azurecr.io | 443 | Registros de contêineres pessoais e de terceiros |
   | \*.blob.core.windows.net | 443 | Baixar deltas de imagem do registro de contêiner do Azure do armazenamento de BLOBs  | 
   | \*.azure-devices.net | 5671, 8883, 443 | Acesso ao Hub IoT |
   | \*.docker.io  | 443 | Acesso ao Hub do Docker (opcional) |

Algumas dessas regras de firewall são herdadas do registro de contêiner do Azure. Para obter mais informações, consulte [configurar regras para acessar um registro de contêiner do Azure atrás de um firewall](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>Configurar a comunicação por meio de um proxy

Se seus dispositivos forem implantados em uma rede que usa um servidor proxy, eles precisam ser capazes de se comunicar através do proxy para alcançar o Hub IoT e registros de contêiner. Para obter mais informações, consulte [configurar um dispositivo de IOT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Gerenciamento de soluções

* **Complementos**
    * Configurar Logs e diagnósticos
    * Considerar os testes e pipelines de CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Configurar Logs e diagnósticos

No Linux, o daemon de IoT Edge usa diários como o driver de log padrão. Você pode usar a ferramenta de linha de comando `journalctl` para consultar os logs de daemon. No Windows, o daemon IoT Edge usa o diagnóstico do PowerShell. Use `Get-IoTEdgeLog` para consultar logs do daemon. IoT Edge módulos usam o driver JSON para registro em log, que é o padrão.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Ao testar uma implantação de IoT Edge, você geralmente pode acessar seus dispositivos para recuperar logs e solucionar problemas. Em um cenário de implantação, talvez você não tenha essa opção. Considere como você vai reunir informações sobre seus dispositivos em produção. Uma opção é usar um módulo de registro em log que coleta informações de outros módulos e as envia para a nuvem. Um exemplo de um módulo de registro [em log é logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics)ou você pode criar o seu próprio. 

### <a name="place-limits-on-log-size"></a>Posicionar limites no tamanho do log

Por padrão, o mecanismo de contêiner Moby não define limites de tamanho de log de contêiner. Com o tempo, isso pode levar ao dispositivo que está se enchendo com os logs e ficando sem espaço em disco. Considere as seguintes opções para evitar isso:

**Opção: definir limites globais que se aplicam a todos os módulos de contêiner**

Você pode limitar o tamanho de todos os arquivos de log de contêiner nas opções de registro do mecanismo de contêiner. O exemplo a seguir define o driver de log para `json-file` (recomendado) com limites de tamanho e número de arquivos:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Adicione (ou acrescente) essas informações a um arquivo chamado `daemon.json` e coloque-o no local certo para a plataforma do dispositivo.

| Plataforma | Localização |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

O mecanismo de contêiner deve ser reiniciado para que as alterações entrem em vigor.

**Opção: ajustar as configurações de log para cada módulo de contêiner**

Você pode fazer isso em **criaroptions** de cada módulo. Por exemplo:

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

**Opções adicionais em sistemas Linux**

* Configure o mecanismo de contêiner para enviar logs para o [diário](https://docs.docker.com/config/containers/logging/journald/) `systemd` definindo `journald` como o driver de log padrão. 

* Remova periodicamente os logs antigos do seu dispositivo instalando uma ferramenta logrotate. Use a seguinte especificação de arquivo: 

   ```
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

### <a name="consider-tests-and-cicd-pipelines"></a>Considerar os testes e pipelines de CI/CD

Para obter o cenário de implantação de IoT Edge mais eficiente, considere integrar sua implantação de produção em seu teste e pipelines de CI/CD. O Azure IoT Edge dá suporte a várias plataformas de CI/CD, incluindo o Azure DevOps. Para obter mais informações, consulte [integração contínua e implantação contínua para Azure IOT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a [implantação automática do IOT Edge](module-deployment-monitoring.md).
* Veja como IoT Edge dá suporte à [integração contínua e à implantação contínua](how-to-ci-cd.md).
