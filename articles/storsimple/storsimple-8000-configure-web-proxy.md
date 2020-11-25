---
title: Configurar o dispositivo da série StorSimple 8000 Microsoft Docs
description: Saiba como utilizar o Windows PowerShell para storSimple para configurar as definições de procuração web para o seu dispositivo StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 48671e7558ea1bd613d33372c96fa3c563407e81
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017224"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Configure o proxy web para o seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial descreve como utilizar o Windows PowerShell para storSimple configurar e visualizar as definições de procuração web para o seu dispositivo StorSimple. As definições de procuração web são utilizadas pelo dispositivo StorSimple quando comunicam com a nuvem. Um servidor de procuração web é usado para adicionar outra camada de segurança, conteúdo de filtro, cache para facilitar os requisitos de largura de banda ou até mesmo ajudar com a análise.

A orientação neste tutorial aplica-se apenas aos dispositivos físicos da série StorSimple 8000. A configuração de procuração web não é suportada no StorSimple Cloud Appliance (8010 e 8020).

O proxy web é uma configuração _opcional_ para o seu dispositivo StorSimple. Só pode configurar o proxy web através do Windows PowerShell para StorSimple. A configuração é um processo em duas etapas da seguinte forma:

1. Configurar primeiro as definições de procuração web através do assistente de configuração ou do Windows PowerShell para cmdlets StorSimple.
2. Em seguida, ative as definições de procuração web configuradas através do Windows PowerShell para cmdlets StorSimple.

Depois de concluída a configuração de procuração web, pode visualizar as definições de procuração web configuradas tanto no serviço Microsoft Azure StorSimple Device Manager como no Windows PowerShell para StorSimple.

Depois de ler este tutorial, poderá:

* Configure o representante web utilizando o assistente de configuração e os cmdlets.
* Ativar o proxy web utilizando cmdlets.
* Ver configurações de procuração web no portal Azure.
* Erros de resolução de problemas durante a configuração de procuração web.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Configure o proxy web via Windows PowerShell para StorSimple

Utiliza qualquer uma das seguintes configurações para configurar configurações de procuração web:

* Faça uma configuração do assistente para guiá-lo através dos passos de configuração.
* Cmdlets em Windows PowerShell para StorSimple.

Cada um destes métodos é discutido nas seguintes secções.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Configure o representante web através do assistente de configuração

Utilize o assistente de configuração para guiá-lo através dos passos para a configuração de procuração web. Execute os seguintes passos para configurar o proxy web no seu dispositivo.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Para configurar o representante web através do assistente de configuração

1. No menu de consola em série, escolha a opção 1, **faça login com acesso total** e forneça a senha do administrador do **dispositivo.** Digite o seguinte comando para iniciar uma sessão de assistente de configuração:
   
    `Invoke-HcsSetupWizard`
2. Se esta for a primeira vez que utiliza o assistente de configuração para o registo do dispositivo, tem de configurar todas as definições de rede necessárias até chegar à configuração de procuração web. Se o seu dispositivo já estiver registado, aceite todas as definições de rede configuradas até chegar à configuração de procuração web. No assistente de configuração, quando solicitado para configurar as definições de procuração web, escreva **Sim**.
3. Para o **URL de procuração web,** especifique o endereço IP ou o nome de domínio totalmente qualificado (FQDN) do seu servidor de procuração web e o número da porta TCP que gostaria que o seu dispositivo usasse ao comunicar com a nuvem. Utilize o seguinte formato:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Por predefinição, é especificado o número da porta TCP 8080.
4. Escolha o tipo de autenticação como **NTLM,** **Básico,** ou **Nenhum**. O básico é a autenticação menos segura para a configuração do servidor proxy. O NT LAN Manager (NTLM) é um protocolo de autenticação altamente seguro e complexo que utiliza um sistema de mensagens de três vias (às vezes quatro se for necessária integridade adicional) para autenticar um utilizador. A autenticação predefinida é NTLM. Para obter mais informações, consulte a autenticação [Básica](https://hc.apache.org/httpclient-3.x/authentication.html) e [NTLM.](https://hc.apache.org/httpclient-3.x/authentication.html) 
   
   > [!IMPORTANT]
   > **No serviço StorSimple Device Manager, as tabelas de monitorização do dispositivo não funcionam quando a autenticação Basic ou NTLM é ativada na configuração do servidor proxy para o dispositivo. Para que as tabelas de monitorização funcionem, é necessário garantir que a autenticação está definida para NENHUM.**
  
5. Se ativou a autenticação, forneça um nome de **utilizador web proxy** e uma **palavra-passe de procuração web**. Também precisa confirmar a senha.
   
    ![Configure o Proxy Web no StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

Se estiver a registar o seu dispositivo pela primeira vez, continue com o registo. Se o seu dispositivo já estava registado, o assistente sai. As definições configuradas são guardadas.

O proxy web está agora ativado. Pode saltar o passo [de procuração da Web e](#enable-web-proxy) ir diretamente para ver as [definições de procuração web no portal Azure](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Configure o representante web via Windows PowerShell para cmdlets StorSimple

Uma forma alternativa de configurar as definições de procuração web é através do Windows PowerShell para cmdlets StorSimple. Execute os seguintes passos para configurar o proxy web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Para configurar o representante web através de cmdlets
1. No menu de consola em série, escolha a opção 1, **faça login com acesso total**. Quando solicitado, forneça a **palavra-passe do administrador do dispositivo**. A palavra-passe predefinida é `Password1` .
2. Na linha de comandos, escreva:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Forneça e confirme a palavra-passe quando solicitado.
   
    ![Configure o Proxy Web no StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

O representante da web está agora configurado e precisa de ser ativado.

## <a name="enable-web-proxy"></a>Ativar o proxy web

O proxy web é desativado por padrão. Depois de configurar as definições de procuração web no seu dispositivo StorSimple, utilize o Windows PowerShell para StorSimple para ativar as definições de procuração web.

> [!NOTE]
> **Este passo não é necessário se utilizar o assistente de configuração para configurar o representante web. O proxy web é automaticamente ativado por predefinição após uma sessão de assistente de configuração.**


Execute os seguintes passos no Windows PowerShell para storSimple para permitir o proxy web no seu dispositivo:

#### <a name="to-enable-web-proxy"></a>Para ativar o proxy web
1. No menu de consola em série, escolha a opção 1, **faça login com acesso total**. Quando solicitado, forneça a **palavra-passe do administrador do dispositivo**. A palavra-passe predefinida é `Password1` .
2. Na linha de comandos, escreva:
   
    `Enable-HcsWebProxy`
   
    Ativou agora a configuração de procuração web no seu dispositivo StorSimple.
   
    ![Configure o Proxy Web no StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Ver configurações de procuração web no portal Azure

As definições de procuração web são configuradas através da interface Windows PowerShell e não podem ser alteradas a partir do portal. No entanto, pode visualizar estas definições configuradas no portal. Execute os seguintes passos para visualizar o proxy da web.

#### <a name="to-view-web-proxy-settings"></a>Para ver as definições de procuração web
1. Navegue para **o serviço StorSimple Device Manager > Dispositivos**. Selecione e clique num dispositivo e, em seguida, vá para **as definições do Dispositivo > Rede**.

    ![Clique em Rede](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. Na lâmina **de definições de rede,** clique no azulejo **proxy web.**

    ![Clique no proxy da web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. Na lâmina **de procuração** web, reveja as definições de procuração web configuradas no seu dispositivo StorSimple.
   
    ![Ver configurações de procuração web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Erros durante a configuração de procuração web

Se as definições de procuração web estiverem configuradas incorretamente, as mensagens de erro são apresentadas ao utilizador no Windows PowerShell para StorSimple. A tabela seguinte explica algumas destas mensagens de erro, as suas causas prováveis e as ações recomendadas.

| Série não. | Código de erro HRESULT | Possível causa de raiz | Ação recomendada |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |O comando é executado a partir do controlador passivo e não é capaz de comunicar com o controlador ativo. |Executar o comando no controlador ativo. Para executar o comando a partir do controlador passivo, tem de fixar a conectividade de controlador passivo para controlador ativo. Tem de ativar o Microsoft Support se esta conectividade estiver quebrada. |
| 2. |0x800710dd - O identificador de operação não é válido |As definições proxy não são suportadas no StorSimple Cloud Appliance. |As definições proxy não são suportadas no StorSimple Cloud Appliance. Estes só podem ser configurados num dispositivo físico StorSimple. |
| 3. |0x80070057 - Parâmetro inválido |Um dos parâmetros previstos para as definições de procuração não é válido. |O URI não é fornecido em formato correto. Utilize o seguinte formato: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - Servidor RPC não disponível |A causa principal é uma das seguintes:</br></br>O aglomerado não está de pé. </br></br>O serviço datapath não está a funcionar.</br></br>O comando é executado a partir do controlador passivo e não é capaz de comunicar com o controlador ativo. |Insaem o Microsoft Support para garantir que o cluster está em funcionamento e o serviço datapath está em execução.</br></br>Executar o comando a partir do controlador ativo. Se pretender executar o comando a partir do controlador passivo, deve certificar-se de que o controlador passivo pode comunicar com o controlador ativo. Tem de ativar o Microsoft Support se esta conectividade estiver quebrada. |
| 5. |0x800706be - Chamada RPC falhou |O aglomerado está em baixo. |Engate o Microsoft Support para garantir que o cluster está em cima. |
| 6. |0x8007138f - Recurso de cluster não encontrado |O recurso de cluster de serviço de plataforma não é encontrado. Isto pode acontecer quando a instalação não foi adequada. |Pode ser necessário fazer um reset de fábrica no seu dispositivo. Pode ser necessário criar um recurso de plataforma. Contacte o Suporte da Microsoft para saber quais os próximos passos. |
| 7. |0x8007138c - Recurso de cluster não online |Os recursos de plataforma ou de cluster datapath não estão online. |Contacte o Microsoft Support para ajudar a garantir que o datapath e o recurso de serviço da plataforma estão online. |

> [!NOTE]
> * A lista acima referida de mensagens de erro não é exaustiva.
> * Erros relacionados com as definições de procuração web não serão apresentados no portal Azure no seu serviço StorSimple Device Manager. Se houver um problema com o proxy da web após a configuração ser concluída, o estado do dispositivo mudará para **Offline** no portal clássico./

## <a name="next-steps"></a>Passos Seguintes
* Se sentir algum problema durante a implementação do seu dispositivo ou configurar as definições de procuração web, consulte a [resolução de problemas da implementação do seu dispositivo StorSimple](./storsimple-8000-troubleshoot-deployment.md).
* Para aprender a utilizar o serviço StorSimple Device Manager, vá utilizar [o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).