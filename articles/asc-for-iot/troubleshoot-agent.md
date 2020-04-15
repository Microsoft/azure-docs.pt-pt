---
title: Start-up do agente de segurança de resolução de problemas (Linux)
description: Problemas a trabalhar com o Centro de Segurança Azure para agentes de segurança iot para linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 935a99dd34b0a4e3d4970e8d91f9332d2bc1489a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310558"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Guia de resolução de problemas do agente de segurança (Linux)

Este artigo explica como resolver potenciais problemas no processo de arranque do agente de segurança.

O Centro de Segurança Azure para o agente IoT inicia-se imediatamente após a instalação. O processo de arranque do agente inclui a leitura da configuração local, a ligação ao Hub Azure IoT e a recuperação da configuração dupla remota. A falha em qualquer uma destas etapas pode fazer com que o agente de segurança falhe.

Neste guia de resolução de problemas, aprenderá a:

> [!div class="checklist"]
> * Validar se o agente de segurança estiver a funcionar
> * Obtenha erros do agente de segurança
> * Compreender e remediar erros do agente de segurança

## <a name="validate-if-the-security-agent-is-running"></a>Validar se o agente de segurança estiver a funcionar

1. Para validar está o agente de segurança em funcionamento, aguarde alguns minutos depois de instalar o agente e executar o seguinte comando.
     <br>

    **Agente C**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **C# agente**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. Se o comando devolver uma linha vazia, o agente de segurança não foi capaz de começar com sucesso.

## <a name="force-stop-the-security-agent"></a>Força parar o agente de segurança

Nos casos em que o agente de segurança não possa arrancar, pare o agente com o seguinte comando e, em seguida, continue para a tabela de erros abaixo:

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>Obtenha erros do agente de segurança

1. Recupere os erros do agente de segurança executando o seguinte comando:

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. O comando de erro do agente de segurança obtém todos os registos criados pelo Centro de Segurança Azure para agente IoT. Utilize a tabela seguinte para compreender os erros e tomar as medidas corretas para a reparação.

> [!Note]
> Os registos de erro são mostrados por ordem cronológica. Certifique-se de que nota a marca de cada erro para ajudar a sua reparação.

## <a name="restart-the-agent"></a>Reiniciar o agente

1. Depois de localizar e corrigir um erro do agente de segurança, tente reiniciar o agente executando o seguinte comando.

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Repita o processo anterior para recuperar o stop e recuperar os erros se o agente continuar a falhar no processo de arranque.

## <a name="understand-security-agent-errors"></a>Compreender erros do agente de segurança

A maioria dos erros do agente de segurança são apresentados no seguinte formato:

```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| Código de Erro | Código sub error | Detalhes do erro | Remediar C | Remediar C # |
|:-----------|:---------------|:--------|:------------|:------------|
| Configuração local | Configuração em falta | Falta uma configuração no ficheiro de configuração local. A mensagem de erro deve indicar qual a chave que falta. | Adicione a chave em falta ao ficheiro /var/LocalConfiguration.json, consulte a [referência cs-localconfig](azure-iot-security-local-configuration-c.md) para obter mais detalhes.| Adicione a chave em falta ao ficheiro General.config, consulte a [referência c#-localconfig](azure-iot-security-local-configuration-csharp.md) para obter detalhes. |
| Configuração local | Configuração Cant Parse | Um valor de configuração não pode ser analisado. A mensagem de erro deve indicar qual a chave que não pode ser analisada. Um valor de configuração não pode ser analisado porque o valor não está no tipo esperado, ou o valor está fora de alcance. | Fixe o valor da chave no ficheiro /var/LocalConfiguration.json de modo a corresponder ao esquema de Configuração Local, consulte a [referência c#-localconfig](azure-iot-security-local-configuration-csharp.md) para obter detalhes. |  Fixe o valor da chave no ficheiro General.config de modo a corresponder ao esquema, consulte a [referência cs-localconfig](azure-iot-security-local-configuration-c.md) para obter detalhes.|
| Configuração local | Formato do Ficheiro | Falhou na análise do ficheiro de configuração. | O ficheiro de configuração é corrompido, descarregue o agente e reinstale. | |
| Configuração remota | Tempo limite | O agente não conseguiu obter o módulo de segurança azul gémea dentro do período de tempo. | Certifique-se de que a configuração da autenticação está correta e tente novamente. | O agente não conseguiu obter o módulo de segurança azul gémea dentro do período de tempo. | Certifique-se de que a configuração da autenticação está correta e tente novamente. |
| Autenticação | Arquivo Não Existe | O ficheiro no caminho dado não existe. | Certifique-se de que o ficheiro existe no caminho dado ou vá ao ficheiro **LocalConfiguration.json** e altere a configuração **Do FicheiroPath.** | Certifique-se de que o ficheiro existe no caminho dado ou vá para o ficheiro **Autenticação.config** e altere a configuração **do ficheiroPath.**|
| Autenticação | Autorização de Arquivo | O agente não tem permissões suficientes para abrir o ficheiro. | Dê ao utilizador **asciotagent** ler permissões no ficheiro no caminho dado. | Certifique-se de que o ficheiro está acessível. |
| Autenticação | Formato do Ficheiro | O ficheiro dado não está no formato correto. | Certifique-se de que o ficheiro está no formato correto. Os tipos de ficheiros suportados são .pfx e .pem. | Certifique-se de que o ficheiro é um ficheiro de certificado válido. |
| Autenticação | Não autorizado | O agente não foi capaz de autenticar contra o IoT Hub com as credenciais dadas. | Valide a configuração de autenticação no ficheiro LocalConfigura, passe pela configuração de autenticação e certifique-se de que todos os detalhes estão corretos, valide que o segredo no ficheiro corresponde à identidade autenticada. | Valide a configuração de autenticação em Autenticação.config, passe pela configuração de autenticação e certifique-se de que todos os detalhes estão corretos, e depois valide que o segredo no ficheiro corresponde à identidade autenticada.
| Autenticação | Não encontrado | O dispositivo/módulo foi encontrado. | Valide a configuração de autenticação - certifique-se de que o nome de anfitrião está correto, o dispositivo existe no IoT Hub e tem um módulo twin de segurança azul. |  Valide a configuração de autenticação - certifique-se de que o nome de anfitrião está correto, o dispositivo existe no IoT Hub e tem um módulo twin de segurança azul. |
| Autenticação | Configuração em falta | Falta uma configuração no ficheiro *Autenticação.config.* A mensagem de erro deve indicar qual a chave que falta. | Adicione a chave em falta no ficheiro *LocalConfiguration.json.*| Adicione a chave em falta no ficheiro *Autenticação.config,* consulte a [referência c#-localconfig](azure-iot-security-local-configuration-csharp.md) para obter mais detalhes. |
| Autenticação | Configuração Cant Parse | Um valor de configuração não pode ser analisado. A mensagem de erro deve indicar qual a chave que não pode ser analisada. Um valor de configuração não pode ser analisado porque ou o valor não é do tipo esperado, ou o valor está fora de alcance. |Corrija o valor da chave no ficheiro **LocalConfiguration.json.** |Fixe o valor da chave no ficheiro **Authentication.config** para combinar com o esquema, consulte a [referência cs-localconfig](azure-iot-security-local-configuration-c.md) para obter mais detalhes.|
|

## <a name="restart-the-agent"></a>Reiniciar o agente

1. Depois de localizar e corrigir um erro do agente de segurança, reinicie o agente executando o seguinte comando:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Se necessário, repita os processos anteriores para forçar a paragem do agente e recuperar os erros se o agente continuar a falhar no processo de arranque.

## <a name="next-steps"></a>Passos seguintes

- Leia o Centro de Segurança Azure para a [visão geral](overview.md) do serviço IoT
- Saiba mais sobre o Azure Security Center for IoT [Architecture](architecture.md)
- Ativar o Centro de Segurança Azure para [o serviço](quickstart-onboard-iot-hub.md) IoT
- Leia o Centro de Segurança Azure para o serviço IoT [FAQ](resources-frequently-asked-questions.md)
- Saiba como aceder a dados de [segurança bruta](how-to-security-data-access.md)
- Compreender [recomendações](concept-recommendations.md)
- Compreender [alertas](concept-security-alerts.md) de segurança
