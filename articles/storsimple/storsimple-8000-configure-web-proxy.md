---
title: Configurar procuração web para dispositivo da série StorSimple 8000 [ StorSimple 8000 ] Microsoft Docs
description: Saiba como utilizar o Windows PowerShell para o StorSimple para configurar as definições de proxy web para o seu dispositivo StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 956cf45eb9e246f2e1f917f2bf487ac14deba90e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65204252"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Configure procuração web para o seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial descreve como utilizar o Windows PowerShell para o StorSimple para configurar e visualizar as definições de procuração web para o seu dispositivo StorSimple. As definições de procuração web são utilizadas pelo dispositivo StorSimple ao comunicar com a nuvem. Um servidor de procuração web é usado para adicionar outra camada de segurança, conteúdo de filtro, cache para facilitar os requisitos de largura de banda ou até mesmo ajudar com a análise.

A orientação neste tutorial aplica-se apenas aos dispositivos físicos da série StorSimple 8000. A configuração do proxy web não é suportada no StorSimple Cloud Appliance (8010 e 8020).

O proxy web é uma configuração _opcional_ para o seu dispositivo StorSimple. Só é possível configurar o proxy da web através do Windows PowerShell para o StorSimple. A configuração é um processo em duas etapas da seguinte forma:

1. Primeiro configura as definições de proxy web através do assistente de configuração ou do Windows PowerShell para cmdlets StorSimple.
2. Em seguida, ativa as definições de proxy web configuradas através do Windows PowerShell para cmdlets StorSimple.

Depois de concluída a configuração do proxy web, pode visualizar as definições de proxy configuradas na Web, tanto no serviço Microsoft Azure StorSimple Device Manager como no Windows PowerShell para StorSimple.

Depois de ler este tutorial, poderá:

* Configure o proxy da web utilizando o assistente de configuração e os cmdlets.
* Ativar o proxy da web utilizando cmdlets.
* Ver configurações de procuração web no portal Azure.
* Erros de resolução de problemas durante a configuração do proxy web.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Configure procuração web via Windows PowerShell para StorSimple

Utilize qualquer uma das seguintes definições de procuração web para configurar as definições de procuração web:

* Configurar o assistente para guiá-lo através dos passos de configuração.
* Cmdlets no Windows PowerShell para StorSimple.

Cada um destes métodos é discutido nas seguintes secções.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Configure procuração web através do assistente de configuração

Utilize o assistente de configuração para guiá-lo através dos passos para a configuração do proxy web. Execute os seguintes passos para configurar o proxy web no seu dispositivo.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Para configurar o proxy web através do assistente de configuração

1. No menu de consola em série, escolha a opção 1, **inicie sessão com acesso completo** e forneça a **palavra-passe**do administrador do dispositivo . Digite o seguinte comando para iniciar uma sessão de assistente de configuração:
   
    `Invoke-HcsSetupWizard`
2. Se esta for a primeira vez que utilizou o assistente de configuração para o registo do dispositivo, tem de configurar todas as definições de rede necessárias até chegar à configuração de procuração web. Se o seu dispositivo já estiver registado, aceite todas as definições de rede configuradas até chegar à configuração de procuração web. No assistente de configuração, quando solicitado para configurar as definições de procuração web, **escreva Sim**.
3. Para o **URL proxy web**, especifique o endereço IP ou o nome de domínio totalmente qualificado (FQDN) do seu servidor de procuração web e o número de porta TCP que gostaria que o seu dispositivo usasse quando comunicasse com a nuvem. Utilize o seguinte formato:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Por defeito, a porta TCP número 8080 é especificada.
4. Escolha o tipo de autenticação como **NTLM,** **Basic**, ou **None**. O básico é a autenticação menos segura para a configuração do servidor proxy. NT LAN Manager (NTLM) é um protocolo de autenticação altamente seguro e complexo que utiliza um sistema de mensagens a três (às vezes quatro se for necessária integridade adicional) para autenticar um utilizador. A autenticação predefinida é NTLM. Para mais informações, consulte a autenticação [Basic](https://hc.apache.org/httpclient-3.x/authentication.html) e [NTLM](https://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **No serviço StorSimple Device Manager, as tabelas de monitorização do dispositivo não funcionam quando a autenticação Basic ou NTLM está ativada na configuração do servidor proxy para o dispositivo. Para que as tabelas de monitorização funcionem, é necessário garantir que a autenticação está definida para NENHUMA.**
  
5. Se ativar a autenticação, forneça um nome de **utilizador de procuração web** e uma **palavra-passe de procuração web**. Também precisa confirmar a senha.
   
    ![Configure procuração web no dispositivo storSimple1](./media/storsimple-configure-web-proxy/IC751830.png)

Se estiver a registar o seu dispositivo pela primeira vez, continue com o registo. Se o seu dispositivo já estava registado, o assistente sai. As definições configuradas são guardadas.

O proxy da Web está agora ativado. Pode saltar o passo [de proxy da Web Ativa](#enable-web-proxy) e ir diretamente para ver as definições de [procuração web no portal Azure](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Configure procuração web via Windows PowerShell para storSimple cmdlets

Uma forma alternativa de configurar as definições de proxy web é através do Windows PowerShell para cmdlets StorSimple. Execute os seguintes passos para configurar o proxy web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Para configurar o proxy web via cmdlets
1. No menu de consola em série, escolha a opção 1, **inicie sessão com acesso total**. Quando solicitado, forneça a **palavra-passe**do administrador do dispositivo . A palavra-passe padrão é `Password1`.
2. Na linha de comandos, escreva:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Forneça e confirme a palavra-passe quando solicitada.
   
    ![Configure procuração web no dispositivo StorSimple3](./media/storsimple-configure-web-proxy/IC751831.png)

O proxy web está agora configurado e precisa de ser ativado.

## <a name="enable-web-proxy"></a>Ativar procuração web

O proxy da web é desativado por padrão. Depois de configurar as definições de proxy web no seu dispositivo StorSimple, utilize o Windows PowerShell para o StorSimple para ativar as definições de proxy web.

> [!NOTE]
> **Este passo não é necessário se tiver utilizado o assistente de configuração para configurar o proxy web. O proxy web é ativado automaticamente por padrão após uma sessão de assistente de configuração.**


Execute os seguintes passos no Windows PowerShell para o StorSimple para ativar o proxy web no seu dispositivo:

#### <a name="to-enable-web-proxy"></a>Para ativar o proxy web
1. No menu de consola em série, escolha a opção 1, **inicie sessão com acesso total**. Quando solicitado, forneça a **palavra-passe**do administrador do dispositivo . A palavra-passe padrão é `Password1`.
2. Na linha de comandos, escreva:
   
    `Enable-HcsWebProxy`
   
    Agora ativou a configuração de procuração web no seu dispositivo StorSimple.
   
    ![Configure procuração web no dispositivo StorSimple4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Ver configurações de procuração web no portal Azure

As definições de procuração web são configuradas através da interface Do Windows PowerShell e não podem ser alteradas a partir do portal. Pode, no entanto, visualizar estas definições configuradas no portal. Execute os seguintes passos para visualizar o proxy da web.

#### <a name="to-view-web-proxy-settings"></a>Para ver as definições de procuração web
1. Navegue para **o serviço StorSimple Device Manager > Dispositivos**. Selecione e clique num dispositivo e, em seguida, vá para **as definições do Dispositivo > Rede**.

    ![Clique na Rede](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. Na lâmina de definições da **Rede,** clique no azulejo proxy **Web.**

    ![Clique em procuração web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. Na lâmina de **procuração web,** reveja as definições de proxy configuradas na web no seu dispositivo StorSimple.
   
    ![Ver definições de procuração web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Erros durante a configuração do proxy web

Se as definições de procuração da web estiverem configuradas incorretamente, as mensagens de erro são apresentadas ao utilizador no Windows PowerShell para storSimple. A tabela que se segue explica algumas destas mensagens de erro, as suas causas prováveis e as ações recomendadas.

| Série não. | Código de erro HRESULT | Possível causa de raiz | Ação recomendada |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |O comando é executado a partir do controlador passivo e não é capaz de comunicar com o controlador ativo. |Mande o comando no controlador ativo. Para executar o comando do controlador passivo, deve fixar a conectividade do controlador passivo para o controlador ativo. Tem de envolver o Microsoft Support se esta conectividade estiver quebrada. |
| 2. |0x800710dd - O identificador de operação não é válido |As definições de procuração não são suportadas no StorSimple Cloud Appliance. |As definições de procuração não são suportadas no StorSimple Cloud Appliance. Estes só podem ser configurados num dispositivo físico StorSimple. |
| 3. |0x80070057 - Parâmetro inválido |Um dos parâmetros previstos para as definições de procuração não é válido. |O URI não é fornecido em formato correto. Utilize o seguinte formato:`http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - Servidor RPC não disponível |A causa principal é uma das seguintes:</br></br>O aglomerado não está em cima. </br></br>O serviço datapath não está a funcionar.</br></br>O comando é executado a partir de um controlador passivo e não é capaz de comunicar com o controlador ativo. |Envolva o Suporte da Microsoft para garantir que o cluster está em funcionamento e o serviço datapath está em execução.</br></br>Executar o comando do controlador ativo. Se pretender executar o comando a partir do controlador passivo, deve certificar-se de que o controlador passivo pode comunicar com o controlador ativo. Tem de envolver o Microsoft Support se esta conectividade estiver quebrada. |
| 5. |0x800706be - Chamada RPC falhou |O aglomerado está em baixo. |Envolva o Suporte microsoft para garantir que o cluster está em pé. |
| 6. |0x8007138f - Recurso de cluster não encontrado |O recurso de cluster de serviço de plataforma não é encontrado. Isto pode acontecer quando a instalação não estava correta. |Poderá ser necessário efetuar uma reinstalação de fábrica no seu dispositivo. Pode ser necessário criar um recurso da plataforma. Contacte o Suporte da Microsoft para saber quais os próximos passos. |
| 7. |0x8007138c - Recurso de cluster não online |Os recursos de cluster de plataforma ou datapath não estão online. |Contacte o Microsoft Support para ajudar a garantir que o recurso de serviço datapath e plataforma está online. |

> [!NOTE]
> * A lista acima das mensagens de erro não é exaustiva.
> * Os erros relacionados com as definições de procuração web não serão apresentados no portal Azure no seu serviço StorSimple Device Manager. Se houver algum problema com o proxy da web após a configuração ser concluída, o estado do dispositivo mudará para **Offline** no portal clássico.[

## <a name="next-steps"></a>Passos Seguintes
* Se tiver algum problema ao implementar o seu dispositivo ou configurar as definições de procuração web, consulte [a Troubleshoot a implementação do seu dispositivo StorSimple](storsimple-troubleshoot-deployment.md).
* Para aprender a utilizar o serviço StorSimple Device Manager, vá utilizar [o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

