---
title: Configurar proxy web para dispositivo da série StorSimple 8000 | Documentos da Microsoft
description: Saiba como utilizar o Windows PowerShell para StorSimple para configurar definições de proxy web para o dispositivo StorSimple.
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
ms.openlocfilehash: be5719d2c383c838ef70c6862c1055c3374e05e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362497"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Configurar o proxy web para o dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial descreve como utilizar o Windows PowerShell para StorSimple para configurar e ver as definições de proxy web para o dispositivo StorSimple. As definições de proxy web são utilizadas pelo dispositivo StorSimple ao comunicar com a cloud. Um servidor web proxy é utilizado para adicionar outra camada de segurança, filtro de conteúdo, cache de requisitos de largura de banda de facilidade ou até mesmo ajuda com a análise.

As orientações neste tutorial aplica-se apenas a dispositivos físicos da série StorSimple 8000. Configuração de proxy da Web não é suportada no StorSimple Cloud Appliance (8010 e 8020).

O proxy Web é um _opcional_ configuração do dispositivo StorSimple. É possível configurar proxy web apenas através do Windows PowerShell para StorSimple. A configuração é um processo de dois passos da seguinte forma:

1. Configurar definições de proxy de web através do Assistente de configuração ou o Windows PowerShell para StorSimple cmdlets pela primeira vez.
2. Em seguida, ativar as definições de proxy de web configurado através do Windows PowerShell para StorSimple cmdlets.

Depois de concluída a configuração de proxy da web, pode ver as definições de proxy da web configurado no serviço do Microsoft Azure StorSimple Device Manager e o Windows PowerShell para StorSimple.

Depois de ler este tutorial, será capaz de:

* Configure o proxy de web utilizando o Assistente de configuração e os cmdlets.
* Ative o proxy da web utilizando os cmdlets.
* Ver as definições de proxy da web no portal do Azure.
* Resolva erros durante a configuração de proxy da web.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Configurar o proxy de web através do Windows PowerShell para StorSimple

Utilize o seguinte para configurar definições de proxy da web:

* Assistente de configuração para ajudá-lo durante os passos de configuração.
* Cmdlets no Windows PowerShell para StorSimple.

Cada um desses métodos é abordada nas seções a seguir.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Configurar o proxy da web através do Assistente de configuração

Utilize o Assistente de configuração para orientá-lo através dos passos para configuração de proxy da web. Execute os seguintes passos para configurar o proxy da web no seu dispositivo.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Configurar proxy web através do Assistente de configuração

1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total** e forneça o **palavra-passe de administrador do dispositivo**. Escreva o seguinte comando para iniciar uma sessão de Assistente de configuração:
   
    `Invoke-HcsSetupWizard`
2. Se esta for a primeira vez que utiliza o Assistente de configuração para o registo do dispositivo, terá de configurar todas as definições de rede necessária, até chegar a configuração de proxy da web. Se o seu dispositivo já está registado, aceite todas as definições de rede configuradas até chegar a configuração de proxy da web. No Assistente de configuração, quando lhe for pedido para configurar definições de proxy da web, escreva **Sim**.
3. Para o **URL de Proxy Web**, especifique o endereço IP ou o nome de domínio completamente qualificado (FQDN) do seu servidor de proxy da web e a porta TCP número que pretende que o seu dispositivo para utilizar ao comunicar com a cloud. Utilize o seguinte formato:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Por predefinição, o número da porta TCP 8080 está especificado.
4. Escolha o tipo de autenticação como **NTLM**, **básica**, ou **None**. Básica é a autenticação menos segura para a configuração do servidor proxy. NT LAN Manager (NTLM) é um protocolo de autenticação altamente seguro e complexas que utiliza um sistema de mensagens de três vias (às vezes, quatro se for necessária a adicionais de integridade) para autenticar um usuário. A autenticação padrão é NTLM. Para obter mais informações, consulte [básica](http://hc.apache.org/httpclient-3.x/authentication.html) e [autenticação NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **No serviço StorSimple Device Manager, os gráficos de monitorização de dispositivos não funcionam quando básica ou autenticação NTLM está ativada na configuração do servidor proxy para o dispositivo. Para os gráficos de monitorização funcionar, terá de garantir que a autenticação está definida como NONE.**
  
5. Se ativou a autenticação, forneça uma **o nome de utilizador do Web Proxy** e uma **palavra-passe do Web Proxy**. Também terá de confirmar a palavra-passe.
   
    ![Configurar o Web Proxy Device1 do StorSimple](./media/storsimple-configure-web-proxy/IC751830.png)

Se estiver a registar o seu dispositivo pela primeira vez, avance para o registo. Se o dispositivo já foi registado, o assistente é encerrado. As definições configuradas são guardadas.

Proxy da Web agora está ativado. Pode ignorar o [ativar o proxy da web](#enable-web-proxy) passo e aceda diretamente à [ver as definições de proxy da web no portal do Azure](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Configurar o proxy da web através do Windows PowerShell para StorSimple cmdlets

Outra forma de configurar as definições de proxy da web é através do Windows PowerShell para StorSimple cmdlets. Execute os seguintes passos para configurar o proxy da web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Configurar proxy web através de cmdlets
1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**. Quando solicitado, forneça o **palavra-passe de administrador do dispositivo**. A palavra-passe predefinida é `Password1`.
2. Na linha de comandos, escreva:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Forneça e confirme a palavra-passe quando lhe for pedido.
   
    ![Configurar o Web Proxy Device3 do StorSimple](./media/storsimple-configure-web-proxy/IC751831.png)

O proxy da web já está configurado e tem de ser ativada.

## <a name="enable-web-proxy"></a>Ativar o proxy da web

Proxy da Web está desativado por predefinição. Depois de configurar as definições de proxy da web no dispositivo StorSimple, utilize o Windows PowerShell para StorSimple para ativar as definições de proxy da web.

> [!NOTE]
> **Este passo não é necessário se tiver utilizado o Assistente de configuração para configurar o proxy da web. Proxy da Web é ativado automaticamente por predefinição depois de uma sessão de Assistente de configuração.**


Execute os seguintes passos no Windows PowerShell para StorSimple ativar o proxy da web no seu dispositivo:

#### <a name="to-enable-web-proxy"></a>Para ativar o proxy da web
1. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**. Quando solicitado, forneça o **palavra-passe de administrador do dispositivo**. A palavra-passe predefinida é `Password1`.
2. Na linha de comandos, escreva:
   
    `Enable-HcsWebProxy`
   
    Agora, ativou a configuração de proxy da web no dispositivo StorSimple.
   
    ![Configurar o Web Proxy Device4 do StorSimple](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Ver as definições de proxy da web no portal do Azure

As definições de proxy web estão configuradas por meio da interface do Windows PowerShell e não podem ser alteradas no portal do. No entanto, pode, ver essas definições configuradas no portal. Execute os seguintes passos para ver o proxy da web.

#### <a name="to-view-web-proxy-settings"></a>Para ver as definições de proxy web
1. Navegue para **serviço StorSimple Device Manager > dispositivos**. Selecione e clique num dispositivo e, em seguida, aceda a **definições do dispositivo > rede**.

    ![Clique em rede](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. Na **as definições de rede** painel, clique nas **Web proxy** mosaico.

    ![Clique em web proxy](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. Na **Web proxy** painel, reveja as definições de proxy da web configurado no dispositivo StorSimple.
   
    ![Ver definições de proxy da web](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Erros durante a configuração do proxy web

Se as definições de proxy da web foram configuradas incorretamente, são apresentadas mensagens de erro para o usuário no Windows PowerShell para StorSimple. A tabela seguinte explica algumas das seguintes mensagens de erro, suas causas prováveis e ações recomendadas.

| Não Serial. | Código de erro de HRESULT | Causa possível | Ação recomendada |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Comando é executado a partir do controlador passivo e não conseguir comunicar com o controlador ativo. |Execute o comando no controlador ativo. Para executar o comando de controlador passivo, tem de corrigir a conectividade da passivo para o controlador ativo. Deve fazer Support da Microsoft se essa conectividade está quebrada. |
| 2. |0x800710dd - o identificador de operação não é válido |Definições de proxy não são suportadas no StorSimple Cloud Appliance. |Definições de proxy não são suportadas no StorSimple Cloud Appliance. Apenas podem ser configurados num dispositivo físico StorSimple. |
| 3. |0x80070057 – um parâmetro inválido |Um dos parâmetros fornecidos para que as definições de proxy não é válido. |O URI não é fornecido no formato correto. Utilize o seguinte formato: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706BA - servidor RPC não está disponível |A causa raiz é um dos seguintes:</br></br>Cluster não está ativo. </br></br>Serviço de DataPath não está em execução.</br></br>O comando é executado a partir do controlador passivo e não conseguir comunicar com o controlador ativo. |Interaja com Support da Microsoft para se certificar de que o cluster está ativo e datapath serviço está em execução.</br></br>Execute o comando a partir do controlador ativo. Se quiser executar o comando de controlador passivo, certifique-se de que o controlador passivo consegue comunicar com o controlador ativo. Deve fazer Support da Microsoft se essa conectividade está quebrada. |
| 5. |0x800706be - falhada de chamada RPC |Cluster está inativo. |Interaja com Support da Microsoft para se certificar de que o cluster está ativo. |
| 6. |0x8007138f - recurso de cluster não encontrado |Não foi encontrado o recurso de cluster do serviço de plataforma. Isto pode acontecer quando a instalação não estava correta. |Terá de efetuar uma reposição de fábrica no dispositivo. Terá de criar um recurso de plataforma. Contacte o Suporte da Microsoft para saber quais os próximos passos. |
| 7. |0x8007138c - recurso de cluster não online |Recursos de cluster de plataforma ou datapath não estão online. |Entre em contato com Support da Microsoft para ajudar a garantir que o recurso de serviço de plataforma e datapath estão online. |

> [!NOTE]
> * A lista acima das mensagens de erro não é exaustiva.
> * Erros relacionados com as definições de proxy da web não serão apresentados no portal do Azure no seu serviço StorSimple Device Manager. Se houver um problema com o web proxy depois de concluída a configuração, o estado do dispositivo será alterado para **Offline** no portal clássico. |

## <a name="next-steps"></a>Próximos Passos
* Se encontrar algum problema ao implementar o seu dispositivo ou configurar definições de proxy da web, consulte [resolver problemas relacionados com a implementação de dispositivos do StorSimple](storsimple-troubleshoot-deployment.md).
* Para saber como utilizar o serviço StorSimple Device Manager, aceda à [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

