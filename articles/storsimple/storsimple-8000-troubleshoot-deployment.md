---
title: Problemas StorSimple 8000 problemas de implantação da série [ Problemas] Microsoft Docs
description: Descreve como diagnosticar e corrigir erros que ocorrem quando implementa o StorSimple pela primeira vez.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: f2b454e812db1eea686f82e92841163f1129b6c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267628"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Problemas de resolução de problemas Problemas Problemas Problemas Problemas Problemas De implementação de dispositivos
## <a name="overview"></a>Descrição geral
Este artigo fornece orientações úteis de resolução de problemas para a sua implementação Microsoft Azure StorSimple. Descreve questões comuns, possíveis causas e passos recomendados para ajudá-lo a resolver problemas que pode experimentar quando configura o StorSimple. 

Esta informação aplica-se tanto ao dispositivo físico da série StorSimple 8000 como ao StorSimple Cloud Appliance.

> [!NOTE]
> Problemas relacionados com a configuração do dispositivo que pode enfrentar podem ocorrer quando implementa o dispositivo pela primeira vez, ou podem ocorrer mais tarde, quando o dispositivo estiver operacional. Este artigo centra-se na resolução de problemas pela primeira vez. Para resolver um dispositivo operacional, vá utilizar [a ferramenta Dediagnóstico para resolver um dispositivo operacional](storsimple-8000-diagnostics.md).

Este artigo também descreve as ferramentas para resolução de problemas StorSimple implementações e fornece um exemplo passo-a-passo de resolução de problemas.

## <a name="first-time-deployment-issues"></a>Questões de implantação pela primeira vez
Se tiver algum problema ao implementar o seu dispositivo pela primeira vez, considere o seguinte:

* Se estiver a resolver problemas com um dispositivo físico, certifique-se de que o hardware foi instalado e configurado conforme descrito no [dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instale o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
* Verifique os pré-requisitos para a implantação. Certifique-se de que dispõe de todas as informações descritas na lista de verificação de configuração de [implementação](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Reveja as Notas de Lançamento StorSimple para ver se o problema é descrito. As notas de lançamento incluem sobras para problemas de instalação conhecidos. 

Durante a implementação do dispositivo, os problemas mais comuns que os utilizadores enfrentam ocorrem quando executam o assistente de configuração e quando registam o dispositivo através do Windows PowerShell para o StorSimple. (Utilize o Windows PowerShell para o StorSimple para registar e configurar o seu dispositivo StorSimple. Para obter mais informações sobre o registo do dispositivo, consulte o [Passo 3: Configure e registe o seu dispositivo através do Windows PowerShell para o StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

As seguintes secções podem ajudá-lo a resolver problemas que encontra quando configura o dispositivo StorSimple pela primeira vez.

## <a name="first-time-setup-wizard-process"></a>Processo de assistente de configuração pela primeira vez
Os seguintes passos resumem o processo do assistente de configuração. Para obter informações detalhadas sobre a configuração, consulte [Implementar o seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).

1. Execute o [cmdlet Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) para iniciar o assistente de configuração que irá guiá-lo através dos passos restantes. 
2. Configure a rede: o assistente de configuração permite configurar as definições de rede para a interface de rede DATA 0 no seu dispositivo StorSimple. Estas definições incluem as seguintes:
   * IP virtual (VIP), máscara de sub-rede e gateway – O [cmdlet Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) é executado em segundo plano. Ele configura o endereço IP, a máscara de sub-rede e a porta de entrada para a interface de rede DATA 0 no seu dispositivo StorSimple.
   * Servidor DNS primário – O [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) cmdlet é executado em segundo plano. Ele configura as definições de DNS para a sua solução StorSimple.
   * Servidor NTP – O [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet é executado em segundo plano. Ele configura as definições do servidor NTP para a sua solução StorSimple.
   * Proxy web opcional – O [cmdlet Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) é executado em segundo plano. Define e permite a configuração de procuração web para a sua solução StorSimple.
3. Configurar a palavra-passe: o próximo passo é configurar a palavra-passe do administrador do dispositivo.
   A palavra-passe do administrador do dispositivo é usada para iniciar sessão no seu dispositivo. A palavra-passe predefinida do dispositivo é **Password1**.
        
     > [!IMPORTANT]
     > As palavras-passe são recolhidas antes do registo, mas aplicadas apenas depois de registar com sucesso o dispositivo. Se houver uma falha na aplicação de uma palavra-passe, será solicitado a fornecer novamente a palavra-passe até que sejam recolhidas as palavras-passe necessárias (que satisfaçam os requisitos de complexidade).
     
4. Registe o dispositivo: o passo final é registar o dispositivo com o serviço StorSimple Device Manager a funcionar no Microsoft Azure. O registo requer que [obtenha a chave](storsimple-8000-manage-service.md#get-the-service-registration-key) de registo de serviço do portal Azure e forneça-a no assistente de configuração. **Após a sua registo com sucesso, é-lhe fornecida uma chave de encriptação de dados de serviço. Certifique-se de manter esta chave de encriptação num local seguro, pois será necessário registar todos os dispositivos subsequentes com o serviço.**

## <a name="common-errors-during-device-deployment"></a>Erros comuns durante a implementação do dispositivo
As tabelas que se seguem listam os erros comuns que poderá encontrar quando:

* Configure as definições de rede necessárias.
* Configure as definições opcionais de procuração web.
* Configurar a palavra-passe do administrador do dispositivo.
* Registe o dispositivo.

## <a name="errors-during-the-required-network-settings"></a>Erros durante as definições de rede necessárias
| Não. | Mensagem de erro | Possíveis causas | Ação recomendada |
| --- | --- | --- | --- |
| 1 |Invocação-HcsSetupWizard: Este comando só pode ser executado no controlador ativo. |A configuração estava a ser executada no controlador passivo. |Execute este comando a partir do controlador ativo. Para mais informações, consulte [Identificar um controlador ativo no seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: Dispositivo não está pronto. |Existem problemas com a conectividade da rede no DATA 0. |Verifique a conectividade da rede física no DATA 0. |
| 3 |Invoke-HcsSetupWizard: Existe um conflito de endereços IP com outro sistema na rede (Exceção de HRESULT: 0x80070263). |O IP fornecido para DATA 0 já estava a ser utilizado por outro sistema. |Forneça um novo IP que não esteja a ser utilizado. |
| 4 |Invoke-HcsSetupWizard: Um recurso de cluster falhou. (Exceção do HRESULT: 0x800713AE). |Duplicar VIP. O IP fornecido já está em uso. |Forneça um novo IP que não esteja a ser utilizado. |
| 5 |Invocar-HcsSetupWizard: Endereço IPv4 inválido. |O endereço IP é fornecido num formato incorreto. |Verifique o formato e forneça novamente o seu endereço IP. Para mais informações, consulte [o Ipv4 Addressing][1]. |
| 6 |Invocar-HcsSetupWizard: Endereço IPv6 inválido. |O endereço IP é fornecido num formato incorreto. |Verifique o formato e forneça novamente o seu endereço IP. Para mais informações, consulte [o Ipv6 Addressing][2]. |
| 7 |Invoke-HcsSetupWizard: Não existem mais pontos finais disponíveis a partir do mapper de ponto final. (Exceção do HRESULT: 0x800706D9) |A funcionalidade do cluster não está a funcionar. |[Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para os próximos passos. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Erros durante as definições opcionais de procuração web
| Não. | Mensagem de erro | Possíveis causas | Ação recomendada |
| --- | --- | --- | --- |
| 1 |Invocação-HcsSetupWizard: Parâmetro inválido (Exceção de HRESULT: 0x80070057) |Um dos parâmetros previstos para as definições de procuração não é válido. |O URI não é fornecido no formato correto. Utilize o seguinte formato: http://*\<endereço IP ou FQDN do servidor de procuração web>*:*\<Número de porta TCP>* |
| 2 |Invoke-HcsSetupWizard: Servidor RPC não disponível (Exceção de HRESULT: 0x800706ba) |A causa principal é uma das seguintes:<ol><li>O aglomerado não está de pé.</li><li>O controlador passivo não pode comunicar com o controlador ativo, e o comando é executado a partir de um controlador passivo.</li></ol> |Dependendo da causa principal:<ol><li>[Contacte](storsimple-8000-contact-microsoft-support.md) o Microsoft Support para se certificar de que o cluster está em cima.</li><li>Executar o comando do controlador ativo. Se pretender executar o comando a partir do controlador passivo, terá de garantir que o controlador passivo pode comunicar com o controlador ativo. Terá de [contactar](storsimple-8000-contact-microsoft-support.md) o Microsoft Support se esta conectividade estiver quebrada.</li></ol> |
| 3 |Invocação-HcsSetupWizard: A chamada rPC falhou (Exceção de HRESULT: 0x800706be) |O aglomerado está em baixo. |[Contacte](storsimple-8000-contact-microsoft-support.md) o Microsoft Support para se certificar de que o cluster está em cima. |
| 4 |Invocação-HcsSetupWizard: Recurso de cluster não encontrado (Exceção de HRESULT: 0x8007138f) |O recurso do cluster não é encontrado. Isto pode acontecer quando a instalação não estava correta. |Pode ser necessário redefinir o dispositivo para as definições predefinidas da fábrica. [Contacte](storsimple-8000-contact-microsoft-support.md) o Microsoft Support para criar um recurso de cluster. |
| 5 |Invoke-HcsSetupWizard: Recurso de cluster não on-line (Exceção de HRESULT: 0x8007138c) |Os recursos de cluster não estão online. |[Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para os próximos passos. |

## <a name="errors-related-to-device-administrator-password"></a>Erros relacionados com a palavra-passe do administrador do dispositivo
A palavra-passe do administrador do dispositivo predefinido é **Password1**. Esta palavra-passe expira após o primeiro logon; portanto, terá de usar o assistente de configuração para o alterar. Deve fornecer uma nova senha de administrador de dispositivo quando registar o dispositivo pela primeira vez. 

Certifique-se de que as suas palavras-passe satisfazem os seguintes requisitos:

* A palavra-passe do administrador do seu dispositivo deve ter entre 8 e 15 caracteres.
* As palavras-passe devem conter 3 dos seguintes 4 tipos de caracteres: minúscula, maiúscula, numérica e especial. 
* A sua palavra-passe não pode ser a mesma que as últimas 24 senhas.

Além disso, tenha em mente que as palavras-passe expiram todos os anos e só podem ser alteradas depois de registar com sucesso o dispositivo. Se o registo falhar por qualquer motivo, as palavras-passe não serão alteradas.

Para obter mais informações sobre a palavra-passe do administrador do dispositivo, aceda ao [serviço StorSimple Device Manager para alterar a sua palavra-passe StorSimple](storsimple-8000-change-passwords.md).

Pode encontrar um ou mais erros a seguir ao configurar o administrador do dispositivo e as palavras-passe do StorSimple Snapshot Manager.

| Não. | Mensagem de erro | Ação recomendada |
| --- | --- | --- |
| 1 |A palavra-passe excede o comprimento máximo. |A palavra-passe do administrador do seu dispositivo deve ter entre 8 e 15 caracteres. |
| 2 |A palavra-passe não satisfaz o comprimento exigido. |A palavra-passe do administrador do seu dispositivo deve ter entre 8 e 15 caracteres.|
| 3 |A palavra-passe deve conter caracteres minúsculos. |As palavras-passe devem conter 3 dos seguintes 4 tipos de caracteres: minúscula, maiúscula, numérica e especial. Certifique-se de que a sua palavra-passe satisfaz estes requisitos. |
| 4 |A palavra-passe deve conter caracteres numéricos. |As palavras-passe devem conter 3 dos seguintes 4 tipos de caracteres: minúscula, maiúscula, numérica e especial. Certifique-se de que a sua palavra-passe satisfaz estes requisitos. |
| 5 |A palavra-passe deve conter caracteres especiais. |As palavras-passe devem conter 3 dos seguintes 4 tipos de caracteres: minúscula, maiúscula, numérica e especial. Certifique-se de que a sua palavra-passe satisfaz estes requisitos. |
| 6 |A palavra-passe deve conter 3 dos seguintes 4 tipos de caracteres: maiúsculas, minúsculas, numéricas e especiais. |A sua palavra-passe não contém os tipos de caracteres necessários. Certifique-se de que a sua palavra-passe satisfaz estes requisitos. |
| 7 |O parâmetro não corresponde à confirmação. |Certifique-se de que a sua palavra-passe satisfaz todos os requisitos e que a inseriu corretamente. |
| 8 |A sua palavra-passe não pode corresponder ao padrão. |A palavra-passe padrão é *Password1*. Tem de alterar esta palavra-passe depois de iniciar sessão pela primeira vez. |
| 9 |A palavra-passe que introduziu não corresponde à palavra-passe do dispositivo. Por favor, reescreva a senha. |Verifique a senha e escreva novamente. |

As palavras-passe são recolhidas antes de o dispositivo ser registado, mas só são aplicadas após o registo bem sucedido. O fluxo de trabalho de recuperação da palavra-passe requer que o dispositivo seja registado.

> [!IMPORTANT]
> Em geral, se uma tentativa de aplicar uma palavra-passe falhar, então o software tenta repetidamente recolher a palavra-passe até que seja bem sucedida. Em casos raros, a palavra-passe não pode ser aplicada. Nesta situação, pode registar o dispositivo e proceder, no entanto as palavras-passe não serão alteradas. Pode alterar a palavra-passe do administrador do dispositivo após a inscrição no portal Azure.


Pode redefinir a palavra-passe no portal Azure através do serviço StorSimple Device Manager. Para mais informações, vá alterar [a palavra-passe do administrador do dispositivo](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Errors during device registration (Erros durante o registo de dispositivos)
Utiliza o serviço StorSimple Device Manager a funcionar no Microsoft Azure para registar o dispositivo. Pode encontrar um ou mais dos seguintes problemas durante o registo do dispositivo.

| Não. | Mensagem de erro | Possíveis causas | Ação recomendada |
| --- | --- | --- | --- |
| 1 |Erro 350027: Falhou em registar o dispositivo com o Gestor de Dispositivos StorSimple. | |Espere alguns minutos e tente novamente a operação. Se o problema persistir, [contacte](storsimple-8000-contact-microsoft-support.md)o Suporte da Microsoft . |
| 2 |Erro 350013: Ocorreu um erro no registo do dispositivo. Isto pode dever-se a uma chave de registo de serviço incorreta. | |Por favor, registe o dispositivo com a chave de registo de serviço correta. Para mais informações, consulte [A chave de registo de serviço.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Erro 350063: A autenticação ao serviço StorSimple Device Manager passou, mas o registo falhou. Por favor, tente novamente a operação depois de algum tempo. |Este erro indica que a autenticação com ACS já passou, mas a chamada de registo feita ao serviço falhou. Isto pode ser o resultado de uma falha esporádica na rede. |Se o problema persistir, [contacte o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md). |
| 4 |Erro 350049: O serviço não pôde ser contactado durante o registo. |Quando a chamada é feita para o serviço, é recebida uma exceção web. Em alguns casos, isto pode ser corrigido reexperimentando a operação mais tarde. |Verifique o seu endereço IP e o nome DNS e, em seguida, volte a tentar a operação. Se o problema persistir, contacte o [Microsoft Support.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Erro 350031: O dispositivo já foi registado. | |Não é necessária nenhuma ação. |
| 6 |Erro 350016: Registo do dispositivo falhou. | |Por favor, certifique-se de que a chave de registo está correta. |
| 7 |Invocação-HcsSetupWizard: Ocorreu um erro ao registar o seu dispositivo; isto pode ser devido a um endereço IP incorreto ou ao nome DNS. Por favor, verifique as definições da rede e tente novamente. Se o problema persistir, [contacte](storsimple-8000-contact-microsoft-support.md)o Suporte da Microsoft . (Erro 350050) |Certifique-se de que o seu dispositivo pode ligar para a rede externa. Se não tiver conectividade com a rede externa, o registo poderá falhar com este erro. Este erro pode ser uma combinação de um ou mais dos seguintes:<ul><li>IP incorreto</li><li>Sub-rede incorreta</li><li>Gateway incorreto</li><li>Definições dNS incorretas</li></ul> |Consulte os passos do exemplo de [resolução de problemas passo a passo](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: A operação atual falhou devido a um erro de serviço interno [0x1FBE2]. Por favor, tente novamente a operação depois de algum tempo. Se o problema persistir, contacte o Microsoft Support. |Trata-se de um erro genérico lançado para todos os erros invisíveis do utilizador do serviço ou do agente. A razão mais comum pode ser que a autenticação ACS falhou. Uma possível causa para a falha é que existem problemas com a configuração do servidor NTP e o tempo no dispositivo não está definido corretamente. |Corrija o tempo (se houver problemas) e, em seguida, tente novamente a operação de registo. Se utilizar o comando Set-HcsSystem -Timezone para ajustar o fuso horário, capitalize cada palavra no fuso horário (por exemplo, "Tempo Padrão do Pacífico").  Se este problema persistir, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para os próximos passos. |
| 9 |Aviso: Não foi possível ativar o dispositivo. O administrador do dispositivo e as palavras-passe do StorSimple Snapshot Manager não foram alteradas. |Se o registo falhar, o administrador do dispositivo e as palavras-passe do StorSimple Snapshot Manager não são alteradas. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Tools for troubleshooting StorSimple deployments (Ferramentas para resolver problemas de implementações do StorSimple)
O StorSimple inclui várias ferramentas que pode utilizar para resolver problemas com a sua solução StorSimple. Estas incluem:

* Pacotes de suporte e registos de dispositivos.
* Cmdlets especificamente concebidos para resolução de problemas.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Pacotes de suporte e registos de dispositivos disponíveis para resolução de problemas
Um pacote de suporte contém todos os registos relevantes que podem ajudar a equipa de Suporte do Microsoft com problemas com dispositivos de resolução de problemas. Pode utilizar o Windows PowerShell para o StorSimple para gerar um pacote de suporte encriptado que pode depois partilhar com pessoal de suporte.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Para ver os registos ou o conteúdo do pacote de suporte
1. Utilize o Windows PowerShell para o StorSimple para gerar um pacote de suporte conforme descrito no [Create e gerir um pacote](storsimple-8000-create-manage-support-package.md)de suporte .
2. Descarregue o [script de desencriptação](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente no seu computador cliente.
3. Utilize este [procedimento passo a passo](storsimple-8000-create-manage-support-package.md#edit-a-support-package) para abrir e desencriptar o pacote de suporte.
4. Os registos de pacotes de suporte desencriptados estão em formato etw/etvx. Pode executar os seguintes passos para visualizar estes ficheiros no Windows Event Viewer:
   
   1. Execute o comando **eventvwr** no seu cliente Windows. Isto vai começar o Espectador de Eventos.
   2. No painel **Ações,** clique em **Open Saved Log** e aponte para os ficheiros de registo em formato etvx/etw (o pacote de suporte). Agora pode ver o ficheiro. Depois de abrir o ficheiro, pode clicar à direita e guardar o ficheiro como texto.
      
      > [!IMPORTANT]
      > Também pode utilizar o cmdlet **Get-WinEvent** para abrir estes ficheiros no Windows PowerShell. Para mais informações, consulte [get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) na documentação de referência do Cmdlet do Windows PowerShell.
     
5. Quando os registos abrirem no Visualizador de Eventos, procure os seguintes registos que contenham problemas relacionados com a configuração do dispositivo:
   
   * hcs_pfconfig/Registo Operacional
   * hcs_pfconfig/Config
6. Nos ficheiros de registo, procure cordas relacionadas com as cmdlets chamadas pelo assistente de configuração. Consulte [o processo do assistente de configuração pela primeira vez](#first-time-setup-wizard-process) para obter uma lista destes cmdlets.
7. Se não conseguir descobrir a causa do problema, pode contactar o [Microsoft Support](storsimple-8000-contact-microsoft-support.md) para os próximos passos. Utilize os passos em [Criar um pedido](storsimple-8000-contact-microsoft-support.md#create-a-support-request) de suporte quando contactar o Microsoft Support para obter assistência.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlets disponíveis para resolução de problemas
Utilize os seguintes cmdlets Windows PowerShell para detetar erros de conectividade.

* `Get-NetAdapter`: Utilize este cmdlet para detetar a saúde das interfaces de rede.
* `Test-Connection`: Utilize este cmdlet para verificar a conectividade da rede dentro e fora da rede.
* `Test-HcsmConnection`: Utilize este cmdlet para verificar a conectividade de um dispositivo registado com sucesso.
* `Sync-HcsTime`: Utilize este cmdlet para visualizar o tempo do dispositivo e forçar uma sincronização temporal com o servidor NTP.
* `Enable-HcsPing`e `Disable-HcsPing`: Utilize estes cmdlets para permitir que os anfitriões pingem as interfaces de rede no seu dispositivo StorSimple. Por predefinição, as interfaces de rede StorSimple não respondem aos pedidos de ping.
* `Trace-HcsRoute`: Utilize este cmdlet como uma ferramenta de rastreio de rotas. Envia pacotes para cada router a caminho de um destino final durante um período de tempo, e depois calcula os resultados com base nos pacotes devolvidos de cada lúpulo. Uma `Trace-HcsRoute` vez que mostra o grau de perda de pacote em qualquer router ou link, pode identificar quais routers ou ligações podem estar a causar problemas de rede.
* `Get-HcsRoutingTable`: Utilize este cmdlet para visualizar a mesa de encaminhamento IP local.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Resolução de problemas com o cmdlet Get-NetAdapter
Quando configura as interfaces de rede para uma implementação de dispositivo pela primeira vez, o estado de hardware não está disponível no UI do serviço StorSimple Device Manager porque o dispositivo ainda não está registado no serviço. Além disso, a lâmina de **saúde do Hardware** pode nem sempre refletir corretamente o estado do dispositivo, especialmente se houver problemas que afetam a sincronização do serviço. Nestas situações, pode utilizar `Get-NetAdapter` o cmdlet para determinar a saúde e o estado das interfaces de rede.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Para ver uma lista de todos os adaptadores de rede no seu dispositivo
1. Inicie o Windows PowerShell para StorSimple e, em seguida, escreva `Get-NetAdapter`. 
2. Utilize a saída `Get-NetAdapter` do cmdlet e as seguintes diretrizes para compreender o estado da interface de rede.
   
   * Se a interface estiver saudável e ativada, o estado **ifIndex** é mostrado como **Up**.
   * Se a interface for saudável mas não estiver fisicamente ligada (por um cabo de rede), o **ifIndex** é mostrado como **Desativado**.
   * Se a interface estiver saudável mas não ativada, o estado **ifIndex** é mostrado como **NotPresent**.
   * Se a interface não existir, não aparece nesta lista. O serviço StorSimple Device Manager UI ainda mostrará esta interface num estado falhado.

Para obter mais informações sobre como utilizar este cmdlet, vá ao [Get-NetAdapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps) na referência cmdlet Do Windows PowerShell.

As seguintes secções mostram `Get-NetAdapter` amostras de saída do cmdlet.

 Nestas amostras, o controlador 0 era o controlador passivo, e foi configurado da seguinte forma:

* DATA 0, DATA 1, DATA 2 e DATA 3 interfaces de rede existiam no dispositivo.
* Os cartões de interface de rede DATA 4 e DATA 5 não estavam presentes; portanto, não estão listados na saída.
* O DATA 0 estava ativado.

O controlador 1 era o controlador ativo, e foi configurado da seguinte forma:

* DADOS 0, DADOS 1, DADOS 2, DADOS 3, DADOS 4 e INTERFACES DE REDE DATA 5 existiam no dispositivo.
* O DATA 0 estava ativado.

**Saída da amostra - controlador 0**

Segue-se a saída do controlador 0 (o controlador passivo). DADOS 1, DADOS 2 e DADOS 3 não estão ligados. OS DADOS 4 e OS DADOS 5 não estão listados porque não estão presentes no dispositivo.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Saída da amostra - controlador 1**

Segue-se a saída do controlador 1 (o controlador ativo). Apenas a interface de rede DATA 0 do dispositivo está configurada e a funcionar.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Resolução de problemas com o cmdlet de ligação de teste
Pode utilizar `Test-Connection` o cmdlet para determinar se o seu dispositivo StorSimple pode ligar-se à rede externa. Se todos os parâmetros de rede, incluindo o DNS, estiverem corretamente `Test-Connection` configurados no assistente de configuração, pode utilizar o cmdlet para ping arping um endereço conhecido fora da rede, como outlook.com.

Deve permitir que o ping atire problemas de conectividade com este cmdlet se o ping estiver desativado.

Consulte as seguintes amostras de saída do `Test-Connection` cmdlet.

> [!NOTE]
> Na primeira amostra, o dispositivo é configurado com um DNS incorreto. Na segunda amostra, o DNS está correto.

**Saída da amostra - DNS incorreto**

Na amostra seguinte, não existe saída para os endereços IPV4 e IPV6, o que indica que o DNS não está resolvido. Isto significa que não existe conectividade com a rede externa e que é necessário fornecer um DNS correto.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Saída da amostra - DNS correto**

Na amostra seguinte, o DNS devolve o endereço IPV4, indicando que o DNS está corretamente configurado. Isto confirma que existe conectividade com a rede externa.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Resolução de problemas com o cmdlet Test-HcsmConnection
Utilize `Test-HcsmConnection` o cmdlet para um dispositivo que já esteja ligado e registado no seu serviço StorSimple Device Manager. Este cmdlet ajuda-o a verificar a conectividade entre um dispositivo registado e o serviço correspondente storSimple Device Manager. Pode executar este comando no Windows PowerShell para storSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Para executar o cmdlet Test-HcsmConnection
1. Certifique-se de que o dispositivo está registado.
2. Verifique o estado do dispositivo. Se o dispositivo for desativado, no modo de manutenção ou offline, poderá ver um dos seguintes erros:
   
   * ErrorCode.CiSDeviceDecommissioned – isto indica que o dispositivo foi desativado.
   * ErrorCode.DeviceNotReady – isto indica que o dispositivo está no modo de manutenção.
   * ErrorCode.DeviceNotReady – isto indica que o dispositivo não está online.
3. Verifique se o serviço StorSimple Device Manager está em funcionamento (utilize o cmdlet [Get-ClusterResource).](https://technet.microsoft.com/library/ee461004.aspx) Se o serviço não estiver em funcionamento, poderá ver os seguintes erros:
   
   * Errorcode.cisapplianceagentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – isto indica que houve uma exceção quando executou o Get-ClusterResource.
4. Verifique o token do Serviço de Controlo de Acesso (ACS). Se lançar uma exceção à web, pode ser o resultado de um problema de gateway, de uma autenticação por procuração em falta, de um DNS incorreto ou de uma falha de autenticação. Pode ver os seguintes erros:
   
   * ErrorCode.CiSApplianceGateway – isto indica uma exceção httpStatusCode.BadGateway: o serviço resolver o nome não conseguiu resolver o nome do anfitrião.
   * ErrorCode.CiSApplianceProxy – isto indica uma exceção httpStatusCode.ProxyAuthenticationRequired (código de estado HTTP 407): o cliente não conseguiu autenticar com o servidor proxy.
   * ErrorCode.CiSApplianceDNSError – isto indica uma exceção webExceptionStatus.NameResolutionFail: o serviço resolver o nome não conseguiu resolver o nome do anfitrião.
   * ErrorCode.CiSApplianceACSError – isto indica que o serviço devolveu um erro de autenticação, mas há conectividade.
     
     Se não lançar uma exceção à web, verifique se o ErrorCode.CiSApplianceFailure. Isto indica que o aparelho falhou.
5. Verifique a conectividade do serviço na nuvem. Se o serviço lançar uma exceção web, poderá ver os seguintes erros:
   
   * ErrorCode.CiSApplianceGateway – isto indica uma exceção httpStatusCode.BadGateway: um servidor de procuração intermédio recebeu um mau pedido de outro representante ou do servidor original.
   * ErrorCode.CiSApplianceProxy – isto indica uma exceção httpStatusCode.ProxyAuthenticationRequired (código de estado HTTP 407): o cliente não conseguiu autenticar com o servidor proxy.
   * ErrorCode.CiSApplianceDNSError – isto indica uma exceção webExceptionStatus.NameResolutionFail: o serviço resolver o nome não conseguiu resolver o nome do anfitrião.
   * ErrorCode.CiSApplianceACSError – isto indica que o serviço devolveu um erro de autenticação, mas há conectividade.
     
     Se não lançar uma exceção à web, verifique se o ErrorCode.CiSApplianceSaasServiceError. Isto indica um problema com o serviço StorSimple Device Manager.
6. Verifique a conectividade do Ônibus de Serviço Azure. ErrorCode.CiSApplianceServiceBusError indica que o dispositivo não pode ligar-se ao Autocarro de Serviço.

Os ficheiros de registo CiSCommandletLog0Curr.errlog e CiSAgentsvc0Curr.errlog terão mais informações, tais como detalhes de exceção.

Para obter mais informações sobre como utilizar o cmdlet, vá ao [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) na documentação de referência do Windows PowerShell.

> [!IMPORTANT]
> Pode executar este cmdlet tanto para o controlador ativo como para o controlador passivo.

Consulte as seguintes amostras de saída do `Test-HcsmConnection` cmdlet.

**Saída da amostra – dispositivo registado com sucesso em execução StorSimple Update 3**

      Controller1>Test-HcsmConnection

      Checking device registration state  ... Success
      Device registered successfully

      Checking primary NTP server [time.windows.com] ... Success

      Checking web proxy  ... NOT SET

      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET

      Checking device online  ... Success

      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success

      Checking connectivity from device to service  ... This will take a few minutes.

      Checking connectivity from device to service  ... Success

      Checking connectivity from service to device  ... Success

      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Saída da amostra - dispositivo offline** 

Esta amostra é de um dispositivo que tem um estado **offline** no portal Azure.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

O dispositivo não conseguiu ligar-se utilizando a configuração de procuração web atual. Isto pode ser um problema com a configuração do proxy web ou um problema de conectividade de rede. Neste caso, deve certificar-se de que as definições de procuração da web estão corretas e que os seus servidores de procuração web estão online e acessíveis.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Resolução de problemas com o cmdlet Sync-HcsTime
Utilize este cmdlet para visualizar o tempo do dispositivo. Se o tempo do dispositivo tiver uma compensação com o servidor NTP, pode então utilizar este cmdlet para sincronizar o tempo com o seu servidor NTP.
- Se a compensação entre o dispositivo e o servidor NTP for superior a 5 minutos, verá um aviso. 
- Se a compensação exceder 15 minutos, então o dispositivo irá desligar-se. Ainda pode usar este cmdlet para forçar uma sincronização de tempo. 
- No entanto, se a compensação exceder 15 horas, não será capaz de sincronizar a hora e será mostrada uma mensagem de erro.

**Saída da amostra - sincronização de tempo forçada usando Sync-HcsTime**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Resolução de problemas com os cmdlets Enable-HcsPing e Disable-HcsPing
Utilize estes cmdlets para garantir que as interfaces de rede do seu dispositivo respondam aos pedidos de ping do ICMP. Por predefinição, as interfaces de rede StorSimple não respondem aos pedidos de ping. Usar este cmdlet é a maneira mais fácil de saber se o seu dispositivo está on-line e acessível.

**Saída da amostra - Enable-HcsPing e Disable-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Resolução de problemas com o cmdlet Trace-HcsRoute
Utilize este cmdlet como uma ferramenta de rastreio de rotas. Envia pacotes para cada router a caminho de um destino final durante um período de tempo, e depois calcula os resultados com base nos pacotes devolvidos de cada lúpulo. Como o cmdlet mostra o grau de perda de pacote em qualquer router ou link, pode identificar quais routers ou ligações podem estar a causar problemas de rede.

**Saída de amostra mostrando como rastrear a rota de um pacote com Trace-HcsRoute**

     Controller0>Trace-HcsRoute -Target 10.126.174.25

     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]

     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]

     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Resolução de problemas com o cmdlet Get-HcsRouttableTable
Utilize este cmdlet para visualizar a mesa de encaminhamento para o seu dispositivo StorSimple. Uma tabela de encaminhamento é um conjunto de regras que podem ajudar a determinar onde os pacotes de dados que viajam através de uma rede de Protocolo de Internet (IP) serão direcionados.

A tabela de encaminhamento mostra as interfaces e o portal que encaminha os dados para as redes especificadas. Também dá a métrica de encaminhamento que é o decisor para o caminho tomado para chegar a um determinado destino. Quanto mais baixa a métrica de encaminhamento, maior é a preferência.

Por exemplo, se tiver 2 interfaces de rede, DADOS 2 e DATA 3, ligados à Internet. Se as métricas de encaminhamento para DADOS 2 e DATA 3 forem 15 e 261 respectivamente, então O DATA 2 com a métrica de encaminhamento mais baixa é a interface preferida utilizada para chegar à Internet.

Se estiver a executar o Update 1 no seu dispositivo StorSimple, a interface de rede DATA 0 tem a maior preferência pelo tráfego na nuvem. Isto implica que mesmo que existam outras interfaces ativadas pela nuvem, o tráfego na nuvem seria encaminhado através do DATA 0.

Se executar `Get-HcsRoutingTable` o cmdlet sem especificar quaisquer parâmetros (como mostra o exemplo seguinte), o cmdlet irá fora das tabelas de encaminhamento IPv4 e IPv6. Em alternativa, pode `Get-HcsRoutingTable -IPv4` `Get-HcsRoutingTable -IPv6` especificar ou obter uma tabela de encaminhamento relevante.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================

      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================

      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None

      Controller0>

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Exemplo de resolução de problemas passo a passo StorSimple
O exemplo que se segue mostra a resolução passo a passo de uma implementação StorSimple. No cenário de exemplo, o registo do dispositivo falha com uma mensagem de erro indicando que as definições de rede ou o nome DNS estão incorretos.

A mensagem de erro devolvida é:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

O erro pode ser causado por qualquer um dos seguintes:

* Instalação de hardware incorreta
* Interface de rede defeituosa(s)
* Endereço IP incorreto, máscara de sub-rede, gateway, servidor DNS primário ou procuração web
* Chave de registo incorreta
* Definições de firewall incorretas

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Para localizar e corrigir o problema de registo do dispositivo
1. Verifique a configuração do seu `Invoke-HcsSetupWizard`dispositivo: no controlador ativo, executar .
   
   > [!NOTE]
   > O assistente de configuração deve ser executado no controlador ativo. Para verificar se está ligado ao controlador ativo, veja o banner apresentado na consola em série. O banner indica se está ligado ao controlador 0 ou ao controlador 1 e se o controlador está ativo ou passivo. Para mais informações, vá identificar [um controlador ativo no seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Certifique-se de que o dispositivo está corretamente ligado: verifique a rede de cabos no plano traseiro do dispositivo. A cablagem é específica do modelo do dispositivo. Para mais informações, vá instalar [o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instale o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Se estiver a utilizar portas de rede de 10 GbE, terá de utilizar os adaptadores QSFP-SFP fornecidos e os cabos SFP. Para mais informações, consulte a [lista de cabos, interruptores e transceptores recomendados para as portas de 10 GbE](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Verifique a saúde da interface da rede:
   
   * Utilize o cmdlet Get-NetAdapter para detetar a saúde das interfaces de rede para DATA 0. 
   * Se o link não estiver a funcionar, o estado **ifindex** indicará que a interface está em baixo. Em seguida, terá de verificar a ligação de rede da porta ao aparelho e ao interruptor. Também terá de excluir cabos maus. 
   * Se suspeitar que a porta DATA 0 do controlador ativo falhou, pode confirmar isso ligando-se à porta DATA 0 do controlador 1. Para confirmar isto, desligue o cabo de rede da parte de trás do dispositivo do controlador 0, ligue o cabo ao controlador 1 e, em seguida, volte a executar o cmdlet Get-NetAdapter.
     Se a porta DATA 0 de um controlador falhar, [contacte](storsimple-8000-contact-microsoft-support.md) o Suporte da Microsoft para os próximos passos. Pode ser necessário substituir o controlador no seu sistema.
4. Verifique a conectividade com o interruptor:
   
   * Certifique-se de que as interfaces de rede DATA 0 no controlador 0 e no controlador 1 no seu recinto primário estão na mesma sub-rede. 
   * Verifique o centro ou o router. Normalmente, deve ligar ambos os controladores ao mesmo hub ou router. 
   * Certifique-se de que os interruptores utilizados para a ligação têm DADOS 0 para ambos os controladores no mesmo vLAN.
5. Eliminar quaisquer erros do utilizador:
   
   * Volte a executar o assistente de configuração (executar **Invoke-HcsSetupWizard)** e introduza novamente os valores para se certificar de que não existem erros. 
   * Verifique a chave de registo utilizada. A mesma chave de registo pode ser utilizada para ligar vários dispositivos a um serviço StorSimple Device Manager. Utilize o procedimento em Obter a chave de [registo de serviço](storsimple-8000-manage-service.md#get-the-service-registration-key) para garantir que está a utilizar a chave de registo correta.
     
     > [!IMPORTANT]
     > Se tiver vários serviços em funcionamento, terá de garantir que a chave de registo do serviço adequado é utilizada para registar o dispositivo. Se tiver registado um dispositivo com o serviço StorSimple Device Manager errado, terá de contactar o [Microsoft Support](storsimple-8000-contact-microsoft-support.md) para os próximos passos. Poderá ter de realizar um reset de fábrica do dispositivo (o que pode resultar na perda de dados) para depois ligá-lo ao serviço pretendido.
     > 
     > 
6. Utilize o cmdlet de ligação de teste para verificar se tem conectividade com a rede externa. Para mais informações, vá a [Troubleshoot com o cmdlet](#troubleshoot-with-the-test-connection-cmdlet)de Ligação de Teste .
7. Verifique se há interferência na firewall. Se verificou que as definições de IP virtual (VIP), subnet, gateway e DNS estão todas corretas, e ainda vê problemas de conectividade, então é possível que a sua firewall esteja bloqueando a comunicação entre o seu dispositivo e a rede externa. Tem de garantir que as portas 80 e 443 estão disponíveis no seu dispositivo StorSimple para comunicação de saída. Para mais informações, consulte [os requisitos de Networking para o seu dispositivo StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Olhe para os troncos. Aceda a pacotes de suporte e registos de [dispositivos disponíveis para resolução de problemas](#support-packages-and-device-logs-available-for-troubleshooting).
9. Se os passos anteriores não resolverem o problema, [contacte](storsimple-8000-contact-microsoft-support.md) o Microsoft Support para obter assistência.

## <a name="next-steps"></a>Passos seguintes
[Aprenda a utilizar a ferramenta De diagnóstico para resolver um dispositivo StorSimple](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
