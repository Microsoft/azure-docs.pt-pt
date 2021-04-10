---
title: Start-up de agente de segurança de resolução de problemas (Linux)
description: Resolução de problemas a trabalhar com o Azure Defender para agentes de segurança IoT para o Linux.
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 9c9c36b822ab6acb9f9a48d4ba809ad32f6f4695
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782593"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Guia de resolução de problemas do agente de segurança (Linux)

Este artigo explica como resolver potenciais problemas no processo de arranque do agente de segurança.

Azure Defender para agente IoT auto-inicia-se imediatamente após a instalação. O processo de arranque do agente inclui a leitura da configuração local, a ligação ao Azure IoT Hub e a recuperação da configuração gémea remota. A falha em qualquer um destes passos pode fazer com que o agente de segurança falhe.

Neste guia de resolução de problemas você vai aprender a:

- Validar se o agente de segurança estiver em execução
- Obtenha erros de agente de segurança
- Compreender e remediar erros do agente de segurança

## <a name="validate-if-the-security-agent-is-running"></a>Validar se o agente de segurança estiver em execução

1. Para validar é que o agente de segurança está em execução, aguarde alguns minutos depois de instalar o agente e executar o seguinte comando.
     <br>

    **Agente C**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **Agente C#**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. Se o comando retornar uma linha vazia, o agente de segurança não foi capaz de começar com sucesso.

## <a name="force-stop-the-security-agent"></a>Forçar a parar o agente de segurança

Nos casos em que o agente de segurança não pode iniciar, pare o agente com o seguinte comando e continue para a tabela de erros abaixo:

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>Obtenha erros de agente de segurança

1. Recuperar erros do agente de segurança executando o seguinte comando:

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. O comando de erro do agente de segurança obtém todos os registos criados pelo Defender para agente IoT. Utilize a tabela seguinte para compreender os erros e tomar as medidas corretas para a reparação.

> [!Note]
> Os registos de erro são apresentados por ordem cronológica. Certifique-se de que regista a hora de cada erro para ajudar na sua reparação.

## <a name="restart-the-agent"></a>Reiniciar o agente

1. Depois de localizar e corrigir um erro do agente de segurança, tente reiniciar o agente executando o seguinte comando.

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Repita o processo anterior para recuperar o stop e recuperar os erros se o agente continuar a falhar no processo de arranque.

## <a name="understand-security-agent-errors"></a>Compreender erros do agente de segurança

A maioria dos erros do agente de segurança são apresentados no seguinte formato:

```
Defender for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| Código de Erro | Sub-código de erro | Detalhes do erro | Remediar C | Remediar C # |
|--|--|--|--|--|
| Configuração local | Configuração em falta | Falta uma configuração no ficheiro de configuração local. A mensagem de erro deve indicar qual a chave que falta. | Adicione a chave em falta no ficheiro /var/LocalConfiguration.js, consulte a [referência cs-localconfig](azure-iot-security-local-configuration-c.md) para obter mais detalhes. | Adicione a chave em falta no ficheiro General.config, consulte a [referência c#-localconfig para](azure-iot-security-local-configuration-csharp.md) obter mais detalhes. |
| Configuração local | Configuração de Parse Cant | Um valor de configuração não pode ser analisado. A mensagem de erro deve indicar qual a chave que não pode ser analisada. Um valor de configuração não pode ser analisado também porque o valor não está no tipo esperado, ou o valor está fora de alcance. | Fixe o valor da chave em /var/LocalConfiguration.jsno ficheiro de modo a que corresponda ao esquema de Configuração Local, consulte a [referência c#-localconfig](azure-iot-security-local-configuration-csharp.md) para obter mais detalhes. | Fixe o valor da chave no ficheiro General.config para que corresponda ao esquema, consulte a [referência cs-localconfig](azure-iot-security-local-configuration-c.md) para obter mais detalhes. |
| Configuração local | Formato do Ficheiro | Falhou em analisar o ficheiro de configuração. | O ficheiro de configuração é corrompido, descarrega o agente e reinstala-se. | - |
| Configuração remota | Tempo Limite | O agente não conseguiu obter o módulo de azureiotsecurity gémeo dentro do período de tempo limite. | Certifique-se de que a configuração da autenticação está correta e tente novamente. | O agente não conseguiu obter o módulo de azureiotsecurity gémeo dentro do período de tempo. Certifique-se de que a configuração da autenticação está correta e tente novamente. |
| Autenticação | Arquivo não existe | O ficheiro no caminho dado não existe. | Certifique-se de que o ficheiro existe no caminho dado ou vá para o **LocalConfiguration.jsno** ficheiro e altere a configuração **do FilePath.** | Certifique-se de que o ficheiro existe no caminho dado ou vá para o ficheiro **Authentication.config** e altere a configuração **do ficheiroPata.** |
| Autenticação | Permissão de arquivo | O agente não tem permissões suficientes para abrir o ficheiro. | Dê ao utilizador **asciotagent** que leia permissões no ficheiro no caminho dado. | Certifique-se de que o ficheiro está acessível. |
| Autenticação | Formato do Ficheiro | O ficheiro dado não está no formato correto. | Certifique-se de que o ficheiro está no formato correto. Os tipos de ficheiros suportados são .pfx e .pem. | Certifique-se de que o ficheiro é um ficheiro de certificado válido. |
| Autenticação | Não autorizado | O agente não foi capaz de autenticar contra o IoT Hub com as credenciais dadas. | Validar a configuração de autenticação no ficheiro Configuração Local, analisar a configuração de autenticação e certificar-se de que todos os detalhes estão corretos, validar que o segredo no ficheiro corresponde à identidade autenticada. | Valide a configuração de autenticação em Authentication.config, percorra a configuração de autenticação e certifique-se de que todos os detalhes estão corretos e, em seguida, valide que o segredo no ficheiro corresponde à identidade autenticada. |
| Autenticação | Não Encontrado | O dispositivo/módulo foi encontrado. | Validar a configuração de autenticação - certifique-se de que o nome de anfitrião está correto, o dispositivo existe no IoT Hub e tem um módulo duplo de azureiotsecurity. | Validar a configuração de autenticação - certifique-se de que o nome de anfitrião está correto, o dispositivo existe no IoT Hub e tem um módulo duplo de azureiotsecurity. |
| Autenticação | Configuração em falta | Falta uma configuração no ficheiro *Authentication.config.* A mensagem de erro deve indicar qual a chave que falta. | Adicione a chave que falta à *LocalConfiguration.jsno* ficheiro. | Adicione a chave em falta no ficheiro *Authentication.config,* consulte a [referência c#-localconfig](azure-iot-security-local-configuration-csharp.md) para obter mais detalhes. |
| Autenticação | Configuração de Parse Cant | Um valor de configuração não pode ser analisado. A mensagem de erro deve indicar qual a chave que não pode ser analisada. Um valor de configuração não pode ser analisado porque ou o valor não é do tipo esperado, ou o valor está fora de alcance. | Fixe o valor da chave no **LocalConfiguration.jsno** ficheiro. | Fixe o valor da chave no ficheiro **Authentication.config** para combinar com o esquema, consulte a [referência cs-localconfig](azure-iot-security-local-configuration-c.md) para obter mais detalhes.|

## <a name="next-steps"></a>Passos seguintes

- Leia o Defender para o serviço IoT [Visão geral](overview.md)
- Saiba mais sobre o Defender for IoT [Architecture](architecture.md)
- Ativar o [Serviço](quickstart-onboard-iot-hub.md) Defender para IoT
- Leia o Defender para o serviço IoT [FAQ](resources-frequently-asked-questions.md)
- Saiba como aceder aos [dados de segurança bruta](how-to-security-data-access.md)
- Compreender [recomendações](concept-recommendations.md)
- Compreender [alertas de](concept-security-alerts.md) segurança
