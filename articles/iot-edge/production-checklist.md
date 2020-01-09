---
title: Prepare-se para implantar sua solução em produção – Azure IoT Edge
description: Saiba como levar sua solução de Azure IoT Edge de desenvolvimento para produção, incluindo a configuração de seus dispositivos com os certificados apropriados e como fazer um plano de implantação para futuras atualizações de código.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1213ab4c623cb1d475dff1d71e65439b1d08f5c1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429440"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Preparar para implantar sua solução de IoT Edge em produção

Quando estiver pronto para levar a sua solução de IoT Edge do desenvolvimento para a produção, certifique-se de que está configurado para o desempenho em curso.

As informações fornecidas neste artigo não são iguais. Para ajudar a priorizar, cada seção começa com listas que dividem o trabalho em duas seções: **importantes** seja concluída antes de entrar em produção, ou **útil** saber.

## <a name="device-configuration"></a>Configuração do dispositivo

Dispositivos IoT Edge podem ser qualquer coisa, desde um Raspberry Pi para um laptop a uma máquina virtual em execução num servidor. Talvez tenha acesso ao dispositivo fisicamente ou por meio de uma conexão virtual ou pode ser isolado por longos períodos de tempo. De qualquer forma, você quer ter certeza de que ele está configurado para funcionar adequadamente. 

* **Importante**
    * Instalar certificados de produção
    * Ter um plano de gestão de dispositivos
    * Utilizar Moby como o mecanismo de contentor

* **Úteis**
    * Selecione o protocolo a montante

### <a name="install-production-certificates"></a>Instalar certificados de produção

Todos os dispositivos do IoT Edge em produção precisam de um certificado de autoridade (CA) de certificado do dispositivo instalado nele. Esse certificado de AC, em seguida, é declarado para o runtime do IoT Edge no ficheiro config.yaml. Para tornar o desenvolvimento e a realização dos testes, o IoT Edge o tempo de execução cria certificados temporários se não existem certificados são declarados no ficheiro config.yaml. No entanto, estes certificados temporários expiram depois de três meses e não são seguros para cenários de produção. 

Para compreender a função do certificado de AC do dispositivo, veja [como o Azure IoT Edge utiliza certificados](iot-edge-certs.md).

Para obter mais informações sobre como instalar certificados em um dispositivo IoT Edge e referenciá-los do arquivo config. YAML, consulte [instalar certificados de produção em um dispositivo IOT Edge](how-to-create-transparent-gateway.md). 

### <a name="have-a-device-management-plan"></a>Ter um plano de gestão de dispositivos

Antes de colocar todos os dispositivos na produção deve saber como vai gerir as atualizações futuras. Para um dispositivo IoT Edge, pode incluir a lista de componentes para atualizar:

* Firmware do dispositivo
* Bibliotecas de sistema operativo
* Motor de contentor, como o Moby
* Daemon de IoT Edge
* Certificados AC

Para obter mais informações, consulte [atualizar o tempo de execução de IOT Edge](how-to-update-iot-edge.md). Os métodos atuais para atualizar o daemon de IoT Edge requerem física ou acesso SSH para o dispositivo do IoT Edge. Se você tiver muitos dispositivos para atualizar, considere adicionar as etapas de atualização a um script ou usar uma ferramenta de automação como Ansible.

### <a name="use-moby-as-the-container-engine"></a>Utilizar Moby como o mecanismo de contentor

Um mecanismo de contêiner é um pré-requisito para qualquer dispositivo de IoT Edge. Motor de moby apenas é suportado na produção. Outros motores de contentor, como o Docker, fazer o trabalho com o IoT Edge e há problema para utilizar estes mecanismos para o desenvolvimento. O mecanismo de moby pode ser redistribuído quando utilizado com o Azure IoT Edge e a Microsoft fornece para esse mecanismo de manutenção.

### <a name="choose-upstream-protocol"></a>Selecione o protocolo a montante

O protocolo (e, portanto, a porta usada) para comunicação upstream com o Hub IoT pode ser configurado para o agente de IoT Edge e o Hub de IoT Edge. O protocolo predefinido é AMQP, mas poderá alterá-lo consoante a configuração de rede. 

Os dois módulos de runtime ambas têm uma **UpstreamProtocol** variável de ambiente. Os valores válidos para a variável são: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configure a variável UpstreamProtocol para o agente de IoT Edge no arquivo config. YAML no próprio dispositivo. Por exemplo, se o dispositivo de IoT Edge estiver protegido por um servidor proxy que bloqueia as portas AMQP, talvez seja necessário configurar o agente de IoT Edge para usar o AMQP sobre WebSocket (AMQPWS) para estabelecer a conexão inicial com o Hub IoT. 

Assim que se liga o seu dispositivo IoT Edge, certifique-se de que continuar a configurar a variável de UpstreamProtocol para ambos os módulos de tempo de execução em implementações futuras. Um exemplo desse processo é fornecido no [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Implementação

* **Úteis**
    * Ser consistente com o protocolo a montante
    * Configurar o armazenamento de host para módulos do sistema
    * Reduzir o espaço de memória usado pelo hub de IoT Edge
    * Não utilize versões de depuração de imagens de módulo

### <a name="be-consistent-with-upstream-protocol"></a>Ser consistente com o protocolo a montante

Se você tiver configurado o agente de IoT Edge em seu dispositivo de IoT Edge para usar um protocolo diferente do AMQP padrão, deverá declarar o mesmo protocolo em todas as implantações futuras. Por exemplo, se o seu dispositivo IoT Edge estiver atrás de um servidor de proxy bloquear portas AMQP, provavelmente configurado ao dispositivo ligar através de AMQP sobre WebSocket (AMQPWS). Ao implantar módulos no dispositivo, configure o mesmo protocolo AMQPWS para o agente de IoT Edge e IoT Edge Hub ou, caso contrário, o AMQP padrão substituirá as configurações e impedirá que você se conecte novamente. 

Você só precisa configurar a variável de ambiente UpstreamProtocol para os módulos agente de IoT Edge e IoT Edge Hub. Quaisquer módulos adicionais adotam qualquer protocolo está definido para os módulos de tempo de execução. 

Um exemplo desse processo é fornecido no [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Configurar o armazenamento de host para módulos do sistema

O Hub IoT Edge e os módulos de agente usam o armazenamento local para manter o estado e habilitar o sistema de mensagens entre módulos, dispositivos e a nuvem. Para melhorar a confiabilidade e o desempenho, configure os módulos do sistema para usar o armazenamento no sistema de arquivos do host.

Para obter mais informações, consulte [host Storage for System modules](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Reduzir o espaço de memória usado pelo hub de IoT Edge

Se estiver implantando dispositivos restritos com memória limitada disponível, você poderá configurar IoT Edge Hub para ser executado em uma capacidade mais simplificada e usar menos espaço em disco. No entanto, essas configurações limitam o desempenho do hub de IoT Edge, portanto, encontre o equilíbrio certo que funciona para sua solução. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Não otimizar o desempenho em dispositivos restritos

O Hub de IoT Edge é otimizado para desempenho por padrão, portanto, ele tenta alocar grandes partes de memória. Esta configuração pode causar problemas de estabilidade em dispositivos menores, como o Raspberry Pi. Se você estiver implantando dispositivos com recursos restritos, talvez queira definir a variável de ambiente **OptimizeForPerformance** como **false** no Hub de IOT Edge. 

Quando **OptimizeForPerformance** é definido como **true**, o cabeçalho do protocolo MQTT usa o PooledByteBufferAllocator que tem melhor desempenho, mas aloca mais memória. O alocador não funciona bem em sistemas operacionais de 32 bits ou em dispositivos com pouca memória. Além disso, quando otimizado para desempenho, o RocksDb aloca mais memória para sua função como o provedor de armazenamento local. 

Para obter mais informações, consulte [problemas de estabilidade no recurso restrita dispositivos](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Desativar os protocolos não utilizados

Outra maneira de otimizar o desempenho do hub de IoT Edge e reduzir seu uso de memória é desativar os cabeçotes de protocolo para quaisquer protocolos que você não esteja usando em sua solução. 

Os cabeçotes de protocolo são configurados Definindo variáveis de ambiente booliano para o módulo IoT Edge Hub em seus manifestos de implantação. As três variáveis são:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Todos os três variáveis tem *dois carateres de sublinhado* e pode ser definido para VERDADEIRO ou FALSO. 

#### <a name="reduce-storage-time-for-messages"></a>Reduzir o tempo de armazenamento de mensagens

O módulo Hub de IoT Edge armazenará mensagens temporariamente se não puderem ser entregues ao Hub IoT por qualquer motivo. Você pode configurar quanto tempo o Hub de IoT Edge mantém em mensagens não entregues antes de deixá-las expirarem. Se você tiver problemas de memória em seu dispositivo, poderá diminuir o valor de **timeToLiveSecs** no módulo de hub de IOT Edge e... 

O valor predefinido do parâmetro timeToLiveSecs é 7200 segundos, que é duas horas. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Não utilize versões de depuração de imagens de módulo

Quando mudar de cenários de teste para cenários de produção, não se esqueça de remover as configurações de depuração de manifestos de implantação. Verifique se nenhuma das imagens de módulo nos manifestos de implantação têm o  **\.depurar** sufixo. Se tiver adicionado criar opções para expor portas nos módulos para depuração, remover aqueles criar opções também. 

## <a name="container-management"></a>Gestão de contentores

* **Importante**
    * Gerir o acesso ao seu registo de contentor
    * Utilizar etiquetas para gerir versões

### <a name="manage-access-to-your-container-registry"></a>Gerir o acesso ao seu registo de contentor

Antes de implementar módulos para dispositivos do IoT Edge de produção, certifique-se de que controlar o acesso ao seu registo de contentor, para que estranhos não conseguir aceder ou fazer alterações em suas imagens de contentor. Utilize um registo de contentor não público, privado para gerir imagens de contentor. 

Os tutoriais e outra documentação, que eu o instruir a utilizar as mesmas credenciais de registo de contentor no seu dispositivo IoT Edge que utiliza no seu computador de desenvolvimento. Estas instruções destinam-se apenas para o ajudar a configurar ambientes de teste e desenvolvimento mais facilmente e não devem ser seguidas num cenário de produção. O Azure Container Registry recomenda [a autenticação com principais de serviço](../container-registry/container-registry-auth-service-principal.md) quando aplicações ou serviços extrair imagens de contentor de uma forma automática automatizada ou de outra forma, tal como dispositivos IoT Edge. Criar um principal de serviço com acesso só de leitura para o seu registo de contentor e fornecer esse nome de utilizador e palavra-passe no manifesto de implantação.

### <a name="use-tags-to-manage-versions"></a>Utilizar etiquetas para gerir versões

Uma marca é um conceito de Docker que você pode usar para distinguir entre versões de contêineres do Docker. As etiquetas são sufixos, como **1.0** que vá no final de um repositório de contentor. Por exemplo, **mcr.microsoft.com/azureiotedge-agent:1.0**. As etiquetas são mutáveis e podem ser alteradas para apontar para outro contêiner em qualquer altura, para que sua equipe deve concordar numa convenção de serem seguidos enquanto atualizar suas imagens de módulo no futuro. 

As etiquetas também ajudam a aplicar atualizações nos seus dispositivos IoT Edge. Quando os emitir uma versão atualizada de um módulo para o seu registo de contentor, incremente a marca. Em seguida, envie uma nova implementação para os seus dispositivos com a marca incrementada. O mecanismo de contentor reconhecerá a marca incrementada, como uma nova versão e irá fazer com que a versão mais recente do módulo para o seu dispositivo. 

Para obter um exemplo de uma convenção de etiqueta, consulte [atualizar o runtime do IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags) para saber como o IoT Edge utiliza a etiquetas sem interrupção e etiquetas específicas, para controlar versões. 

## <a name="networking"></a>Funcionamento em Rede

* **Úteis**
    * Configuração de saída/entrada de revisão
    * Permitir conexões de dispositivos IoT Edge
    * Configurar a comunicação através de um proxy

### <a name="review-outboundinbound-configuration"></a>Configuração de saída/entrada de revisão

Canais de comunicação entre o IoT Hub do Azure e IoT Edge sempre estão configurados para serem saída. Na maioria dos cenários de IoT Edge, apenas três ligações são necessárias. O mecanismo de contentor tem de se ligar com o registo de contentores (ou registos) que contém as imagens de módulo. O runtime do IoT Edge tem de se ligar ao IoT Hub para obter informações de configuração do dispositivo e enviar telemetria e mensagens. E, se utilizar o aprovisionamento automático, o daemon de IoT Edge tem de se ligar ao serviço de aprovisionamento de dispositivos. Para obter mais informações, consulte [Firewall e regras de configuração de porta](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>Permitir conexões de dispositivos IoT Edge

Se sua configuração de rede exigir que você explicitamente permita conexões feitas de dispositivos IoT Edge, examine a seguinte lista de componentes de IoT Edge:

* **Agente do IoT Edge** abre uma ligação AMQP/MQTT persistente para o IoT Hub, possivelmente ao longo de WebSockets. 
* **Hub do IoT Edge** abre uma única ligação AMQP persistente ou várias ligações de MQTT para o IoT Hub, possivelmente ao longo de WebSockets. 
* **O daemon de IoT Edge** faz intermitentes chamadas HTTPS para o IoT Hub. 

Em todos os três casos, o nome DNS deve corresponder ao padrão \*Azure-Devices.NET. 

Além disso, o **mecanismo de contentor** faz chamadas para registos de contentores através de HTTPS. Para obter as imagens de contentor de runtime do IoT Edge, o nome DNS é mcr.microsoft.com. O mecanismo de contentor liga-se aos outros registos conforme configurado na implementação. 

Esta lista de verificação é um ponto de partida para regras de firewall:

   | URL (\* = curinga) | Portas TCP de saída | Utilização |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Registro de contêiner da Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Acesso ao DPS (opcional) |
   | \*.azurecr.io | 443 | Registros de contêineres pessoais e de terceiros |
   | \*.blob.core.windows.net | 443 | Baixar deltas de imagem do registro de contêiner do Azure do armazenamento de BLOBs  | 
   | \*.azure-devices.net | 5671, 8883, 443 | Acesso ao Hub IoT |
   | \*. docker.io  | 443 | Acesso ao Hub do Docker (opcional) |

Algumas dessas regras de firewall são herdadas do registro de contêiner do Azure. Para obter mais informações, consulte [configurar regras para acessar um registro de contêiner do Azure atrás de um firewall](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>Configurar a comunicação através de um proxy

Se os dispositivos estão a ser implementado numa rede que utiliza um servidor proxy, eles precisam ser capazes de comunicar através do proxy para alcançar o IoT Hub e registos de contentores. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Gerenciamento de solução

* **Úteis**
    * Configurar os registos e diagnósticos
    * Considere os testes e pipelines CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Configurar os registos e diagnósticos

No Linux, o daemon de IoT Edge usa diários como o driver de log padrão. Pode usar a ferramenta de linha de comando `journalctl` para consultar o daemon de registos. No Windows, o daemon de IoT Edge usa o diagnóstico do PowerShell. Utilize `Get-IoTEdgeLog` para registos de consulta a partir do daemon. IoT Edge módulos usam o driver JSON para registro em log, que é o padrão.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Quando estiver a testar uma implementação de IoT Edge, normalmente, pode acessar seus dispositivos para obter os registos e resolver problemas. Num cenário de implementação, pode não ter essa opção. Considere como vai para reunir informações sobre os seus dispositivos em produção. Uma opção é utilizar um módulo de registo que recolhe informações de outros módulos e envia-os para a cloud. Um exemplo de um módulo de registo é [logspout loganalytics](https://github.com/veyalla/logspout-loganalytics), ou pode desenhar sua própria. 

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

* Configure o mecanismo de contêiner para enviar logs para `systemd` [diário](https://docs.docker.com/config/containers/logging/journald/) configurando `journald` como o driver de log padrão. 

* Remova periodicamente os logs antigos do seu dispositivo instalando uma ferramenta logrotate. Utilize a especificação do ficheiro seguinte: 

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

### <a name="consider-tests-and-cicd-pipelines"></a>Considere os testes e pipelines CI/CD

Para o cenário de implantação mais eficiente do IoT Edge, considere a integrar a sua implementação de produção em seus testes e os pipelines CI/CD. O Azure IoT Edge suporta várias plataformas de CI/CD, incluindo DevOps do Azure. Para obter mais informações, consulte [integração contínua e implementação contínua para o Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [implementação automática do IoT Edge](module-deployment-monitoring.md).
* Veja como o IoT Edge suporta [integração contínua e implementação contínua](how-to-ci-cd.md).
