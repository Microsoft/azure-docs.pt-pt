---
title: Preparar os dispositivos e implementações para produção - Azure IoT Edge | Documentos da Microsoft
description: Saiba como utilizar a sua solução de Azure IoT Edge do desenvolvimento para produção, incluindo configurar seus dispositivos com os certificados adequados e fazer um plano de implantação para atualizações ao código futuras.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 618414331ab22cff41c7ac02c78f4bef333d0c84
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433455"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Preparar para implantar sua solução de IoT Edge em produção

Quando estiver pronto para levar a sua solução de IoT Edge do desenvolvimento para a produção, certifique-se de que está configurado para o desempenho em curso.

As informações fornecidas neste artigo não são tudo iguais. Para ajudar a priorizar, cada seção começa com listas que dividem o trabalho em duas seções: **importantes** seja concluída antes de entrar em produção, ou **útil** saber.

## <a name="device-configuration"></a>Configuração do dispositivo

Dispositivos IoT Edge podem ser qualquer coisa, desde um Raspberry Pi para um laptop a uma máquina virtual em execução num servidor. Talvez tenha acesso ao dispositivo fisicamente ou por meio de uma conexão virtual ou pode ser isolado por longos períodos de tempo. De qualquer forma, deseja certificar-se de que está configurado para executar adequadamente. 

* **Importante**
    * Instalar certificados de produção
    * Ter um plano de gestão de dispositivos
    * Utilizar Moby como o mecanismo de contentor

* **Úteis**
    * Selecione o protocolo a montante

### <a name="install-production-certificates"></a>Instalar certificados de produção

Todos os dispositivos do IoT Edge em produção precisam de um certificado de autoridade (CA) de certificado do dispositivo instalado nele. Esse certificado de AC, em seguida, é declarado para o runtime do IoT Edge no ficheiro config.yaml. Para tornar o desenvolvimento e a realização dos testes, o IoT Edge o tempo de execução cria certificados temporários se não existem certificados são declarados no ficheiro config.yaml. No entanto, estes certificados temporários expiram depois de três meses e não são seguros para cenários de produção. 

Para compreender a função do certificado de AC do dispositivo, veja [como o Azure IoT Edge utiliza certificados](iot-edge-certs.md).

Para obter mais informações sobre como instalar certificados num dispositivo IoT Edge e referenciá-los a partir do ficheiro config.yaml, consulte [configurar um dispositivo IoT Edge para atuar como gateway transparente](how-to-create-transparent-gateway.md). Os passos para configurar os certificados são os mesmos, se o dispositivo vai ser utilizado como um gateway ou não. Esse artigo oferece scripts para gerar certificados de exemplo apenas para teste. Não use esses certificados de exemplo na produção. 

### <a name="have-a-device-management-plan"></a>Ter um plano de gestão de dispositivos

Antes de colocar todos os dispositivos na produção deve saber como vai gerir as atualizações futuras. Para um dispositivo IoT Edge, pode incluir a lista de componentes para atualizar:

* Firmware do dispositivo
* Bibliotecas de sistema operativo
* Motor de contentor, como o Moby
* Daemon de IoT Edge
* Certificados AC

Para obter passos atualizar o daemon de IoT Edge, veja [atualizar o runtime do IoT Edge](how-to-update-iot-edge.md). Os métodos atuais para atualizar o daemon de IoT Edge requerem física ou acesso SSH para o dispositivo do IoT Edge. Se tiver muitos dispositivos para atualizar, considere adicionar os passos de atualização para um script ou utilizar uma ferramenta de automatização, como o Ansible para realizar atualizações em escala.

### <a name="use-moby-as-the-container-engine"></a>Utilizar Moby como o mecanismo de contentor

Ter um mecanismo de contentor num dispositivo é um pré-requisito para todos os dispositivos IoT Edge. Motor de moby apenas é suportado na produção. Outros motores de contentor, como o Docker, fazer o trabalho com o IoT Edge e há problema para utilizar estes mecanismos para o desenvolvimento. O mecanismo de moby pode ser redistribuído quando utilizado com o Azure IoT Edge e a Microsoft fornece para esse mecanismo de manutenção. Usando outros mecanismos de contentor num dispositivo IoT Edge não é suportada.

### <a name="choose-upstream-protocol"></a>Selecione o protocolo a montante

O protocolo (e, portanto, a porta utilizada) para comunicação a montante para o IoT Hub pode ser configurada para o agente do Edge e hub do Edge. O protocolo predefinido é AMQP, mas poderá alterá-lo consoante a configuração de rede. 

Os dois módulos de runtime ambas têm uma **UpstreamProtocol** variável de ambiente. Os valores válidos para a variável são: 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configure a variável UpstreamProtocol para o agente do Edge no ficheiro config.yaml no próprio dispositivo. Por exemplo, se o seu dispositivo IoT Edge estiver atrás de um servidor de proxy bloquear portas AMQP, terá de configurar o agente do Edge para utilizar o AMQP sobre WebSocket (AMQPWS) para estabelecer a ligação inicial ao IoT Hub. 

Assim que se liga o seu dispositivo IoT Edge, certifique-se de que continuar a configurar a variável de UpstreamProtocol para ambos os módulos de tempo de execução em implementações futuras. Um exemplo desse processo é fornecido no [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Implementação

* **Úteis**
    * Ser consistente com o protocolo a montante
    * Reduzir o espaço de memória utilizado pelo hub do Edge
    * Não utilize versões de depuração de imagens de módulo

### <a name="be-consistent-with-upstream-protocol"></a>Ser consistente com o protocolo a montante

Se tiver configurado o agente do Edge no dispositivo IoT Edge para utilizar um protocolo diferente que não o predefinido, AMQP, em seguida, declare o mesmo protocolo em todas as implementações subsequentes. Por exemplo, se o seu dispositivo IoT Edge estiver atrás de um servidor de proxy bloquear portas AMQP, provavelmente configurado ao dispositivo ligar através de AMQP sobre WebSocket (AMQPWS). Ao implementar módulos no dispositivo, se não configurar o mesmo protocolo APQPWS para o agente do Edge e hub do Edge, o padrão AMQP irá substituir as definições e impedir a ligação novamente. 

Só tem de configurar a variável de ambiente de UpstreamProtocol para o agente do Edge e os módulos de hub do Edge. Quaisquer módulos adicionais adotam qualquer protocolo está definido para os módulos de tempo de execução. 

Um exemplo desse processo é fornecido no [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

### <a name="reduce-memory-space-used-by-edge-hub"></a>Reduzir o espaço de memória utilizado pelo hub do Edge

Se estiver implantando dispositivos restritos com memória limitado disponível, pode configurar o hub do Edge para executar numa capacidade mais simplificada e utilizar menos espaço em disco. Estas configurações de limitar o desempenho do hub do Edge, no entanto, por isso, encontrar o equilíbrio certo que funciona para a sua solução. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Não otimizar o desempenho em dispositivos restritos

O hub do Edge está otimizado para desempenho por predefinição, para que ele tenta alocar grandes segmentos de memória. Esta configuração pode causar problemas de estabilidade em dispositivos menores, como o Raspberry Pi. Se estiver implantando dispositivos com recursos restritos, pode querer definir o **OptimizeForPerformance** variável de ambiente para **falso** no hub do Edge. 

Para obter mais informações, consulte [problemas de estabilidade no recurso restrita dispositivos](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Desativar os protocolos não utilizados

Outra forma de otimizar o desempenho do hub do Edge e reduzir a utilização de memória é desativar a cabeça de protocolo para quaisquer protocolos que não estiver a utilizar na sua solução. 

Cabeça de protocolo é configurada através da definição de variáveis de ambiente booleano para o módulo de hub do Edge em seus manifestos de implantação. As três variáveis são:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Todos os três variáveis tem *dois carateres de sublinhado* e pode ser definido para VERDADEIRO ou FALSO. 

#### <a name="reduce-storage-time-for-messages"></a>Reduzir o tempo de armazenamento de mensagens

O módulo de hub do Edge armazena temporariamente mensagens se eles não podem ser entregue ao IoT Hub por qualquer motivo. Pode configurar o tempo que o hub do Edge se mantém nesse mensagens não serão entregues antes de informá-los de expirar. Se tiver problemas de memória no seu dispositivo, pode reduzir a **timeToLiveSecs** valor no duplo do módulo de hub Edge. 

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

Uma marca é um conceito de Docker que pode utilizar para distinguir entre as versões de contentores do Docker. As etiquetas são sufixos, como **1.0** que vá no final de um repositório de contentor. Por exemplo, **mcr.microsoft.com/azureiotedge-agent:1.0**. As etiquetas são mutáveis e podem ser alteradas para apontar para outro contêiner em qualquer altura, para que sua equipe deve concordar numa convenção de serem seguidos enquanto atualizar suas imagens de módulo no futuro. 

As etiquetas também ajudam a aplicar atualizações nos seus dispositivos IoT Edge. Quando os emitir uma versão atualizada de um módulo para o seu registo de contentor, incremente a marca. Em seguida, envie uma nova implementação para os seus dispositivos com a marca incrementada. O mecanismo de contentor reconhecerá a marca incrementada, como uma nova versão e irá fazer com que a versão mais recente do módulo para o seu dispositivo. 

Para obter um exemplo de uma convenção de etiqueta, consulte [atualizar o runtime do IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags) para saber como o IoT Edge utiliza a etiquetas sem interrupção e etiquetas específicas, para controlar versões. 

## <a name="networking"></a>Redes

* **Úteis**
    * Configuração de saída/entrada de revisão
    * Ligações de lista de permissões
    * Configurar a comunicação através de um proxy

### <a name="review-outboundinbound-configuration"></a>Configuração de saída/entrada de revisão

Canais de comunicação entre o IoT Hub do Azure e IoT Edge sempre estão configurados para serem saída. Na maioria dos cenários de IoT Edge, apenas três ligações são necessárias. O mecanismo de contentor tem de se ligar com o registo de contentores (ou registos) que contém as imagens de módulo. O runtime do IoT Edge tem de se ligar ao IoT Hub para obter informações de configuração do dispositivo e enviar telemetria e mensagens. E, se utilizar o aprovisionamento automático, o daemon de IoT Edge tem de se ligar ao serviço de aprovisionamento de dispositivos. Para obter mais informações, consulte [Firewall e regras de configuração de porta](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="whitelist-connections"></a>Ligações de lista de permissões

Se a configuração de rede necessitar que explicitamente ligações de lista aprovada feita a partir do dispositivos IoT Edge, reveja a lista seguinte de componentes do IoT Edge:

* **Agente do IoT Edge** abre uma ligação AMQP/MQTT persistente para o IoT Hub, possivelmente ao longo de WebSockets. 
* **Hub do IoT Edge** abre uma única ligação AMQP persistente ou várias ligações de MQTT para o IoT Hub, possivelmente ao longo de WebSockets. 
* **O daemon de IoT Edge** faz intermitentes chamadas HTTPS para o IoT Hub. 

Em todos os três casos, o nome DNS deve corresponder ao padrão \*Azure-Devices.NET. 

Além disso, o **mecanismo de contentor** faz chamadas para registos de contentores através de HTTPS. Para obter as imagens de contentor de runtime do IoT Edge, o nome DNS é mcr.microsoft.com. O mecanismo de contentor liga-se aos outros registos conforme configurado na implementação. 

Esta lista de verificação é um ponto de partida para as regras de firewall:

   | URL (\* = carateres universais) | Portas TCP de saída | Utilização |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Registo de contentor da Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Acesso de pontos de distribuição (opcional) |
   | \*.azurecr.io | 443 | Registos de contentores de pessoal e 3rd party |
   | \*.blob.core.windows.net | 443 | Transferência de deltas de imagem | 
   | \*.azure-devices.net | 5671, 8883, 443 | Acesso do IoT Hub |
   | \*. docker.io  | 443 | Acesso de docker (opcional) |

### <a name="configure-communication-through-a-proxy"></a>Configurar a comunicação através de um proxy

Se os dispositivos estão a ser implementado numa rede que utiliza um servidor proxy, eles precisam ser capazes de comunicar através do proxy para alcançar o IoT Hub e registos de contentores. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Gerenciamento de solução

* **Úteis**
    * Configurar os registos e diagnósticos
    * Considere os testes e pipelines CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Configurar os registos e diagnósticos

No Linux, o daemon de IoT Edge utiliza diários como sendo o padrão de registo de controlador. Pode usar a ferramenta de linha de comando `journalctl` para consultar o daemon de registos. No Windows, o daemon de IoT Edge usa o diagnóstico do PowerShell. Utilize `Get-WinEvent` para registos de consulta a partir do daemon. Módulos do IoT Edge utilizam o controlador JSON para o registo, o que é o padrão de Docker.  

Quando estiver a testar uma implementação de IoT Edge, normalmente, pode acessar seus dispositivos para obter os registos e resolver problemas. Num cenário de implementação, pode não ter essa opção. Considere como vai para reunir informações sobre os seus dispositivos em produção. Uma opção é utilizar um módulo de registo que recolhe informações de outros módulos e envia-os para a cloud. Um exemplo de um módulo de registo é [logspout loganalytics](https://github.com/veyalla/logspout-loganalytics), ou pode desenhar sua própria. 

Se estiver preocupado com registos fique demasiado grande num dispositivo de restrição de recursos, tem algumas opções para reduzir a utilização de memória. 

* Especificamente pode limitar o tamanho de todos os logfiles de docker no daemon do Docker em si. Para o Linux, configure o daemon no `/etc/docker/daemon.json`. Para Windows, `C:\ProgramData\docker\confige\daemon.json`. 
* Se quiser ajustar o tamanho de ficheiro de registo para cada contentor, pode fazê-lo no CreateOptions de cada módulo. 
* Configure Docker para gerir automaticamente os registos ao definir diários como o driver de registo predefinido para o Docker. 
* Remova periodicamente registos antigos do seu dispositivo ao instalar uma ferramenta de logrotate do Docker. Utilize a especificação do ficheiro seguinte: 

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

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [implementação automática do IoT Edge](module-deployment-monitoring.md).
* Veja como o IoT Edge suporta [integração contínua e implementação contínua](how-to-ci-cd.md).
