---
title: Resolução de problemas StorSimple 8000 problemas de implantação da série | Microsoft Docs
description: Descreve como diagnosticar e corrigir erros que ocorrem quando implementa storSimple pela primeira vez.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: f1bce5c0ecdab4a9c96c3f2cc0e9734247bdfd66
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566350"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Problemas de resolução de problemas Problemas de implementação de dispositivos StorSimple
## <a name="overview"></a>Descrição geral
Este artigo fornece orientações úteis para a resolução de problemas para a sua implementação Microsoft Azure StorSimple. Descreve problemas comuns, possíveis causas e medidas recomendadas para o ajudar a resolver problemas que poderá experimentar quando configurar storSimple. 

Esta informação aplica-se tanto ao dispositivo físico da série StorSimple 8000 como ao StorSimple Cloud Appliance.

> [!NOTE]
> Os problemas relacionados com a configuração do dispositivo que possa enfrentar podem ocorrer quando se implanta o dispositivo pela primeira vez, ou podem ocorrer mais tarde, quando o dispositivo estiver operacional. Este artigo centra-se na resolução de problemas de implantação pela primeira vez. Para resolver problemas com um dispositivo operacional, vá utilizar [a ferramenta De diagnóstico para resolver problemas num dispositivo operacional](storsimple-8000-diagnostics.md).

Este artigo também descreve as ferramentas para resolver as implementações StorSimple e fornece um exemplo passo-a-passo de resolução de problemas.

## <a name="first-time-deployment-issues"></a>Questões de implantação pela primeira vez
Se tiver um problema ao implementar o seu dispositivo pela primeira vez, considere as seguintes orientações:

* Se estiver a resolver problemas com um dispositivo físico, certifique-se de que o hardware foi instalado e configurado conforme descrito na [Instalação do seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instale o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
* Verifique os pré-requisitos para a implantação. Certifique-se de que tem todas as informações descritas na lista de verificação de [configuração de implementação](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Reveja as notas de lançamento StorSimple para ver se o problema está descrito. As notas de lançamento incluem soluções alternativas para problemas de instalação conhecidos. 

Durante a implementação do dispositivo, os problemas mais comuns que os utilizadores enfrentam ocorrem quando executam o assistente de configuração e quando registam o dispositivo através do Windows PowerShell para StorSimple. (Utilize o Windows PowerShell para o StorSimple registar e configurar o seu dispositivo StorSimple. Para obter mais informações sobre o registo do dispositivo, consulte [o Passo 3: Configurar e registar o seu dispositivo através do Windows PowerShell para StorSimple).](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)

As seguintes secções podem ajudá-lo a resolver problemas que encontra quando configurar o dispositivo StorSimple pela primeira vez.

## <a name="first-time-setup-wizard-process"></a>Processo de assistente de configuração pela primeira vez
Os passos seguintes resumem o processo de assistente de configuração. Para obter informações detalhadas sobre a configuração, consulte [implementar o seu dispositivo StorSimple no local.](storsimple-8000-deployment-walkthrough-u2.md)

1. Executar o cmdlet [Invoke-HcsSetupWizard](/previous-versions/windows/powershell-scripting/dn688135(v=wps.630)) para iniciar o assistente de configuração que irá guiá-lo através dos passos restantes. 
2. Configure a rede: o assistente de configuração permite configurar as definições de rede para a interface de rede DATA 0 no seu dispositivo StorSimple. As seguintes definições estão incluídas:
   * IP virtual (VIP), máscara de sub-rede e gateway – O [cmdlet Set-HcsNetInterface](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630)) é executado em segundo plano. Configura o endereço IP, a máscara de sub-rede e o gateway para a interface de rede DATA 0 no seu dispositivo StorSimple.
   * Servidor PRIMÁRIO DNS – O [cmdlet Set-HcsDnsClientServerAddress](/previous-versions/windows/powershell-scripting/dn688172(v=wps.630)) é executado em segundo plano. Configura as definições de DNS para a sua solução StorSimple.
   * Servidor NTP – O [cmdlet Set-HcsNtpClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) é executado em segundo plano. Configura as definições do servidor NTP para a sua solução StorSimple.
   * Procuração web opcional – O [cmdlet Set-HcsWebProxy](/previous-versions/windows/powershell-scripting/dn688154(v=wps.630)) é executado em segundo plano. Define e permite a configuração de procuração web para a sua solução StorSimple.
3. Configurar a palavra-passe: o próximo passo é configurar a palavra-passe do administrador do dispositivo.
   A palavra-passe do administrador do dispositivo é utilizada para iniciar sessão no seu dispositivo. A palavra-passe predefinida do dispositivo é **Password1**.
        
     > [!IMPORTANT]
     > As palavras-passe são recolhidas antes do registo, mas aplicadas apenas depois de registar com sucesso o dispositivo. Se houver uma falha na aplicação de uma palavra-passe, será solicitado que forneça novamente a palavra-passe até que sejam recolhidas as palavras-passe necessárias (que satisfazem os requisitos de complexidade).
     
4. Registar o dispositivo: o passo final é registar o dispositivo com o serviço StorSimple Device Manager em execução no Microsoft Azure. A inscrição requer que [obtenha a chave](storsimple-8000-manage-service.md#get-the-service-registration-key) de registo de serviço do portal Azure e forneça-a no assistente de configuração. **Depois de o dispositivo ser registado com sucesso, é-lhe fornecida uma chave de encriptação de dados de serviço. Certifique-se de que mantém esta chave de encriptação num local seguro, pois será necessário registar todos os dispositivos futuros com o serviço.**

## <a name="common-errors-during-device-deployment"></a>Erros comuns durante a implantação do dispositivo
As tabelas que se seguem listam erros comuns que poderá encontrar quando:

* Configure as definições de rede necessárias.
* Configure as definições opcionais de procuração web.
* Configurar a senha do administrador do dispositivo.
* Registe o dispositivo.

## <a name="errors-during-the-required-network-settings"></a>Erros durante as definições de rede necessárias
| N.º | Mensagem de erro | Possíveis causas | Ação recomendada |
| --- | --- | --- | --- |
| 1 |Invocar-HcsSetupWizard: Este comando só pode ser executado no controlador ativo. |A configuração estava a ser realizada no controlador passivo. |Executar este comando a partir do controlador ativo. Para obter mais informações, consulte [Identificar um controlador ativo no seu dispositivo.](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device) |
| 2 |Invocar-hcsSetupWizard: Dispositivo não está pronto. |Existem problemas com a conectividade da rede no DATA 0. |Verifique a conectividade da rede física no DATA 0. |
| 3 |Invoke-HcsSetupWizard: Existe um conflito de endereços IP com outro sistema na rede (Exceção da HRESULT: 0x80070263). |O IP fornecido para DATA 0 já estava a ser utilizado por outro sistema. |Forneça um novo IP que não esteja em uso. |
| 4 |Invoke-HcsSetupWizard: Um recurso de cluster falhou. (Exceção da HRESULT: 0x800713AE). |Duplicar VIP. O IP fornecido já está em uso. |Forneça um novo IP que não esteja em uso. |
| 5 |Invoke-HcsSetupWizard: Endereço IPv4 inválido. |O endereço IP é fornecido num formato incorreto. |Verifique o formato e volte a fornecer o seu endereço IP. Para mais informações, consulte [o Endereço Ipv4.][1] |
| 6 |Invoke-HcsSetupWizard: Endereço IPv6 inválido. |O endereço IP é fornecido num formato incorreto. |Verifique o formato e volte a fornecer o seu endereço IP. Para mais informações, consulte [o Endereço Ipv6.][2] |
| 7 |Invoke-HcsSetupWizard: Não existem mais pontos finais disponíveis a partir do mapper do ponto final. (Exceção da HRESULT: 0x800706D9) |A funcionalidade do cluster não está a funcionar. |[Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para os próximos passos. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Erros durante as definições opcionais de procuração web
| N.º | Mensagem de erro | Possíveis causas | Ação recomendada |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Parâmetro inválido (Exceção de HRESULT: 0x80070057) |Um dos parâmetros previstos para as definições de procuração não é válido. |O URI não é fornecido no formato correto. Utilize o seguinte formato: *\<IP address or FQDN of the web proxy server>* http://:*\<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: servidor RPC não disponível (Exceção de HRESULT: 0x800706ba) |A causa principal é uma das seguintes:<ol><li>O aglomerado não está de pé.</li><li>O controlador passivo não pode comunicar com o controlador ativo, e o comando é executado a partir do controlador passivo.</li></ol> |Dependendo da causa raiz:<ol><li>[Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para se certificar de que o cluster está em cima.</li><li>Executar o comando a partir do controlador ativo. Se pretender executar o comando a partir do controlador passivo, terá de se certificar de que o controlador passivo pode comunicar com o controlador ativo. Terá de [contactar](storsimple-8000-contact-microsoft-support.md) o Microsoft Support se esta conectividade estiver quebrada.</li></ol> |
| 3 |Invoke-HcsSetupWizard: RPC call failed (Exceção da HRESULT: 0x800706be) |O aglomerado está em baixo. |[Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para se certificar de que o cluster está em cima. |
| 4 |Invoke-HcsSetupWizard: Recurso de cluster não encontrado (Exceção da HRESULT: 0x8007138f) |O recurso de cluster não é encontrado. Isto pode acontecer quando a instalação não estava correta. |Pode ser necessário reiniciar o dispositivo para as definições predefinidos da fábrica. [Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para criar um recurso de cluster. |
| 5 |Invoke-HcsSetupWizard: Recurso de cluster não online (Exceção da HRESULT: 0x8007138c) |Os recursos do cluster não estão online. |[Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para os próximos passos. |

## <a name="errors-related-to-device-administrator-password"></a>Erros relacionados com a palavra-passe do administrador do dispositivo
A palavra-passe do administrador do dispositivo predefinido é **Palavra-passe1**. Esta palavra-passe expira após o primeiro início de são; portanto, terá de utilizar o assistente de configuração para alterá-lo. Tem de fornecer uma nova palavra-passe do administrador do dispositivo quando registar o dispositivo pela primeira vez. 

Certifique-se de que as suas palavras-passe cumprem os seguintes requisitos:

* A palavra-passe do administrador do dispositivo deve ter entre 8 e 15 caracteres.
* As palavras-passe devem conter três dos seguintes tipos de caracteres: minúsculas, maiúsculas, numéricas e especiais. 
* A sua palavra-passe não pode ser a mesma das últimas 24 palavras-passe.

Além disso, tenha em mente que as palavras-passe expiram todos os anos e só podem ser alteradas depois de registar o dispositivo com sucesso. Se o registo falhar por qualquer motivo, as palavras-passe não serão alteradas.

Para obter mais informações sobre a palavra-passe do administrador do dispositivo, [aceda ao serviço StorSimple Device Manager para alterar a sua palavra-passe StorSimple](storsimple-8000-change-passwords.md).

Pode encontrar um ou mais dos seguintes erros ao configurar o administrador do dispositivo e as palavras-passe do StorSimple Snapshot Manager.

| N.º | Mensagem de erro | Ação recomendada |
| --- | --- | --- |
| 1 |A palavra-passe excede o comprimento máximo. |A palavra-passe do administrador do dispositivo deve ter entre 8 e 15 caracteres de comprimento. |
| 2 |A palavra-passe não corresponde ao comprimento exigido. |A palavra-passe do administrador do dispositivo deve ter entre 8 e 15 caracteres de comprimento.|
| 3 |A palavra-passe deve conter caracteres minúsculos. |As palavras-passe devem conter 3 dos seguintes 4 tipos de caracteres: minúsculas, maiúsculas, numéricas e especiais. Certifique-se de que a sua palavra-passe satisfaz estes requisitos. |
| 4 |A palavra-passe deve conter caracteres numéricos. |As palavras-passe devem conter 3 dos seguintes 4 tipos de caracteres: minúsculas, maiúsculas, numéricas e especiais. Certifique-se de que a sua palavra-passe satisfaz estes requisitos. |
| 5 |A palavra-passe deve conter caracteres especiais. |As palavras-passe devem conter 3 dos seguintes 4 tipos de caracteres: minúsculas, maiúsculas, numéricas e especiais. Certifique-se de que a sua palavra-passe satisfaz estes requisitos. |
| 6 |A palavra-passe deve conter 3 dos seguintes 4 tipos de caracteres: maiúsculas, minúsculas, numéricas e especiais. |A sua palavra-passe não contém os tipos de caracteres necessários. Certifique-se de que a sua palavra-passe satisfaz estes requisitos. |
| 7 |O parâmetro não corresponde à confirmação. |Certifique-se de que a sua palavra-passe satisfaz todos os requisitos e que a introduziu corretamente. |
| 8 |A sua palavra-passe não pode corresponder ao padrão. |A palavra-passe predefinida é *Palavra-passe1*. Tem de alterar esta palavra-passe depois de iniciar sessão pela primeira vez. |
| 9 |A palavra-passe que introduziu não corresponde à palavra-passe do dispositivo. Por favor, retipe a senha. |Verifique a senha e escreva-a novamente. |

As palavras-passe são recolhidas antes de o dispositivo ser registado, mas são aplicadas apenas após o registo bem sucedido. O fluxo de trabalho de recuperação de palavras-passe requer que o dispositivo seja registado.

> [!IMPORTANT]
> Em geral, se uma tentativa de aplicação de uma palavra-passe falhar, o software tenta repetidamente recolher a palavra-passe até ser bem sucedida. Em casos raros, a palavra-passe não pode ser aplicada. Nesta situação, pode registar o dispositivo e proceder, no entanto as palavras-passe não serão alteradas. Pode alterar a palavra-passe do administrador do dispositivo após o registo a partir do portal Azure.


Pode redefinir a palavra-passe no portal Azure através do serviço StorSimple Device Manager. Para mais informações, aceda à [palavra-passe do administrador do dispositivo.](storsimple-8000-change-passwords.md#change-the-device-administrator-password)

## <a name="errors-during-device-registration"></a>Errors during device registration (Erros durante o registo de dispositivos)
Utilize o serviço StorSimple Device Manager em execução no Microsoft Azure para registar o dispositivo. Pode encontrar um ou mais dos seguintes problemas durante o registo do dispositivo.

| N.º | Mensagem de erro | Possíveis causas | Ação recomendada |
| --- | --- | --- | --- |
| 1 |Erro 350027: Não registou o dispositivo com o Gestor de Dispositivos StorSimple. | |Aguarde alguns minutos e tente a operação novamente. Se o problema persistir, contacte o [Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 2 |Erro 350013: Ocorreu um erro no registo do dispositivo. Isto pode dever-se a uma chave de registo de serviço incorreta. | |Registe novamente o aparelho com a chave de registo de serviço correta. Para mais informações, consulte [obter a chave de registo do serviço.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Erro 350063: A autenticação ao serviço StorSimple Device Manager passou mas o registo falhou. Por favor, recaia a operação depois de algum tempo. |Este erro indica que a autenticação com ACS passou, mas a chamada de registo feita para o serviço falhou. Isto pode ser o resultado de uma falha esporádica na rede. |Se o problema persistir, contacte o [Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 4 |Erro 350049: O serviço não pôde ser alcançado durante o registo. |Quando a chamada é feita para o serviço, uma exceção web é recebida. Em alguns casos, isto pode ser corrigido redando a operação mais tarde. |Verifique o seu endereço IP e o nome DNS e, em seguida, volte a tentar a operação. Se o problema persistir, contacte o [Microsoft Support.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Erro 350031: O dispositivo já foi registado. | |Não é necessária ação. |
| 6 |Erro 350016: O registo do dispositivo falhou. | |Por favor, certifique-se de que a chave de registo está correta. |
| 7 |Invoke-HcsSetupWizard: Ocorreu um erro ao registar o seu dispositivo; isto pode ser devido a um endereço IP incorreto ou nome DNS. Verifique as definições da sua rede e tente novamente. Se o problema persistir, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md). (Erro 350050) |Certifique-se de que o seu dispositivo pode pingar a rede externa. Se não tiver conectividade com a rede externa, o registo poderá falhar com este erro. Este erro pode ser uma combinação de um ou mais dos seguintes:<ul><li>IP incorreto</li><li>Sub-rede incorreta</li><li>Gateway incorreto</li><li>Definições de DNS incorretas</li></ul> |Consulte os passos no [exemplo passo-a-passo de resolução de problemas](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: A operação atual falhou devido a um erro de serviço interno [0x1FBE2]. Por favor, recaia a operação depois de algum tempo. Se o problema persistir, contacte o Microsoft Support. |Este é um erro genérico lançado para todos os erros invisíveis do utilizador do serviço ou do agente. A razão mais comum pode ser que a autenticação ACS falhou. Uma possível causa para a falha é que existem problemas com a configuração do servidor NTP e o tempo no dispositivo não está definido corretamente. |Corrija a hora (em caso de problemas) e, em seguida, re-tentar a operação de registo. Se utilizar o comando Set-HcsSystem -Fuso Horário para ajustar o fuso horário, capitalize cada palavra no fuso horário (por exemplo, "Tempo Padrão do Pacífico").  Se este problema persistir, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para os próximos passos. |
| 9 |Aviso: Não foi possível ativar o dispositivo. O administrador do seu dispositivo e as palavras-passe do StorSimple Snapshot Manager não foram alteradas. |Se o registo falhar, o administrador do dispositivo e as palavras-passe do StorSimple Snapshot Manager não serão alteradas. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Tools for troubleshooting StorSimple deployments (Ferramentas para resolver problemas de implementações do StorSimple)
O StorSimple inclui várias ferramentas que pode utilizar para resolver problemas na sua solução StorSimple. Estas ferramentas incluem:

* Suporte pacotes e registos de dispositivos.
* Cmdlets projetados para resolução de problemas.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Pacotes de suporte e registos de dispositivos disponíveis para resolução de problemas
Um pacote de suporte contém todos os registos relevantes que podem ajudar a equipa de Suporte do Microsoft com problemas no dispositivo de resolução de problemas. Pode utilizar o Windows PowerShell para storSimple para gerar um pacote de suporte encriptado que pode partilhar com o pessoal de suporte.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Para ver os registos ou o conteúdo do pacote de suporte
1. Utilize o Windows PowerShell para o StorSimple gerar um pacote de suporte descrito no [Create e gerir um pacote de suporte](storsimple-8000-create-manage-support-package.md).
2. Descarregue o [script de desencriptação](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente no computador do seu cliente.
3. Utilize este [procedimento passo a passo](storsimple-8000-create-manage-support-package.md#edit-a-support-package) para abrir e desencriptar o pacote de suporte.
4. Os registos de pacotes de suporte desencriptados estão no formato ETW/etvx. Pode executar os seguintes passos para visualizar estes ficheiros no Windows Event Viewer:
   
   1. Executar o comando **eventvwr** no seu cliente Windows para iniciar o Visualizador de Eventos.
   2. No painel **de Ações,** clique em **Abrir RegistoS Guardados** e aponte para os ficheiros de registo no formato etvx/etw (o pacote de suporte). Agora pode ver o ficheiro. Depois de abrir o ficheiro, pode clicar à direita e guardar o ficheiro como texto.
      
      > [!IMPORTANT]
      > Também pode utilizar o **cmdlet Get-WinEvent** para abrir estes ficheiros no Windows PowerShell. Para obter mais informações, consulte [o Get-WinEvent](/powershell/module/microsoft.powershell.diagnostics/get-winevent) na documentação de referência do Cmdlet Windows PowerShell.
     
5. Quando os registos abrirem no Visualizador de Eventos, procure os seguintes registos que contenham problemas relacionados com a configuração do dispositivo:
   
   * hcs_pfconfig/Registo Operacional
   * hcs_pfconfig/Config
6. Nos ficheiros de registo, procure cordas relacionadas com os cmdlets chamados pelo assistente de configuração. Consulte [o processo de assistente de configuração pela primeira vez](#first-time-setup-wizard-process) para obter uma lista destes cmdlets.
7. Se não conseguir descobrir a causa do problema, pode contactar o [Microsoft Support](storsimple-8000-contact-microsoft-support.md) para os próximos passos. Utilize os passos em [Criar um pedido de suporte](storsimple-8000-contact-microsoft-support.md#create-a-support-request) quando contactar o Microsoft Support para obter assistência.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlets disponíveis para resolução de problemas
Utilize os seguintes cmdlets Windows PowerShell para detetar erros de conectividade.

* `Get-NetAdapter`: Utilize este cmdlet para detetar a saúde das interfaces de rede.
* `Test-Connection`: Utilize este cmdlet para verificar a conectividade da rede dentro e fora da rede.
* `Test-HcsmConnection`: Utilize este cmdlet para verificar a conectividade de um dispositivo registado com sucesso.
* `Sync-HcsTime`: Utilize este cmdlet para visualizar o tempo do dispositivo e force uma sincronização temporal com o servidor NTP.
* `Enable-HcsPing` e `Disable-HcsPing` : Utilize estes cmdlets para permitir que os anfitriões coloquem as interfaces de rede no seu dispositivo StorSimple. Por predefinição, as interfaces de rede StorSimple não respondem aos pedidos de ping.
* `Trace-HcsRoute`: Utilize este cmdlet como ferramenta de rastreio de rotas. Envia pacotes para cada router a caminho de um destino final durante um período de tempo, e depois calcula os resultados com base nos pacotes devolvidos de cada salto. Uma `Trace-HcsRoute` vez que mostra o grau de perda de pacote em qualquer router ou link dado, você pode localizar quais routers ou links podem estar causando problemas de rede.
* `Get-HcsRoutingTable`: Utilize este cmdlet para visualizar a tabela de encaminhamento IP local.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Resolução de problemas com o Get-NetAdapter cmdlet
Quando configurar interfaces de rede para uma primeira implementação de dispositivos, o estado do hardware não está disponível no UI do Gestor de Dispositivos StorSimple porque o dispositivo ainda não está registado no serviço. Além disso, a lâmina **de saúde** do Hardware pode nem sempre refletir corretamente o estado do dispositivo, especialmente se existirem problemas que afetam a sincronização do serviço. Nestas situações, pode utilizar o `Get-NetAdapter` cmdlet para determinar a saúde e o estado das suas interfaces de rede.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Para ver uma lista de todos os adaptadores de rede no seu dispositivo
1. Iniciar o Windows PowerShell para StorSimple e, em seguida, escrever `Get-NetAdapter` . 
2. Utilize a saída do `Get-NetAdapter` cmdlet e as seguintes diretrizes para compreender o estado da sua interface de rede.
   
   * Se a interface estiver saudável e ativada, o estado **ifIndex** é mostrado como **Up**.
   * Se a interface for saudável mas não estiver fisicamente ligada (por um cabo de rede), o **ifIndex** é mostrado como **Desativado**.
   * Se a interface for saudável mas não ativada, o estado **ifIndex** é apresentado como **NotPresent**.
   * Se a interface não existir, não aparece nesta lista. O UI do gestor de dispositivos StorSimple continuará a mostrar esta interface num estado falhado.

Para obter mais informações sobre como utilizar este cmdlet, aceda ao [Get-NetAdapter](/powershell/module/netadapter/get-netadapter) na referência cmdlet Windows PowerShell.

As secções seguintes mostram amostras de saída do `Get-NetAdapter` cmdlet.

 Nestas amostras, o controlador 0 era o controlador passivo, e foi configurado da seguinte forma:

* Data 0, DATA 1, DATA 2 e DATA 3 existiam no dispositivo.
* Os cartões de interface de rede DATA 4 e DATA 5 não estavam presentes; portanto, não estão listados na saída.
* O DATA 0 estava ativado.

O controlador 1 era o controlador ativo, e foi configurado da seguinte forma:

* DATA 0, DATA 1, DATA 2, DATA 3, DATA 4 e DATA 5 existiam no dispositivo.
* O DATA 0 estava ativado.

**Saída da amostra - controlador 0**

Os seguintes dados da amostra são a saída do controlador 0 (o controlador passivo). OS DADOS 1, DATA 2 e DATA 3 não estão ligados. Os DADOS 4 e OS DADOS 5 não estão listados porque não estão presentes no dispositivo.

```output
Controller0>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
Ethernet 2           HCS VNIC                                    13       Up
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
```


**Saída da amostra - controlador 1**

Os seguintes dados da amostra são a saída do controlador 1 (o controlador ativo). Apenas a interface de rede DATA 0 do dispositivo está configurada e a funcionar.

```output
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
```


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Resolução de problemas com o Test-Connection cmdlet
Pode utilizar o `Test-Connection` cmdlet para determinar se o seu dispositivo StorSimple pode ligar-se à rede externa. Se todos os parâmetros de rede, incluindo o DNS, estiverem configurados corretamente no assistente de configuração, pode utilizar o `Test-Connection` cmdlet para pingar um endereço conhecido fora da rede, como outlook.com.

Se o cmdlet ping estiver desativado, deve ativar o ping para ser utilizado em problemas de conectividade de resolução de problemas.

Consulte as seguintes amostras de saída do `Test-Connection` cmdlet.

> [!NOTE]
> Na primeira amostra, o dispositivo é configurado com um DNS incorreto. Na segunda amostra, o DNS está correto.

**Saída da amostra – DNS incorreto**

A amostra que se segue não inclui qualquer saída para os endereços IPV4 e IPV6, o que indica que o DNS não está resolvido. Não há conectividade com a rede externa, e um DNS correto precisa ser fornecido.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
```

**Saída da amostra – DNS correto**

Na amostra seguinte, o DNS devolve o endereço IPV4, indicando que o DNS está configurado corretamente. A saída confirma que existe conectividade com a rede externa.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
```

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Resolução de problemas com o Test-HcsmConnection cmdlet
Utilize o `Test-HcsmConnection` cmdlet para um dispositivo que já está ligado e registado com o seu serviço StorSimple Device Manager. Este cmdlet ajuda-o a verificar a conectividade entre um dispositivo registado e o serviço de Gestor de Dispositivos StorSimple correspondente. Pode executar este comando no Windows PowerShell para StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Para executar o Test-HcsmConnection cmdlet
1. Certifique-se de que o dispositivo está registado.
2. Verifique o estado do dispositivo. Se o dispositivo for desativado, em modo de manutenção ou offline, poderá ver um dos seguintes erros:
   
   * ErrorCode.CiSDeviceDecomificado: Indica que o dispositivo está desativado.
   * ErrorCode.DeviceNotReady: Indica que o dispositivo está em modo de manutenção.
   * ErrorCode.DeviceNotReady: Indica que o dispositivo não está online.
3. Verifique se o serviço StorSimple Device Manager está em funcionamento (utilize o cmdlet [Get-ClusterResource).](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461004(v=technet.10)) Se o serviço não estiver a funcionar, poderá ver os seguintes erros:
   
   * ErrorCode.cisApplianceagentNotOnline
   * ErrorCode.CisPowershellScriptHcsError: Indica que houve uma exceção quando executou o Get-ClusterResource.
4. Verifique o sinal do Serviço de Controlo de Acesso (ACS). Se lançar uma exceção web, pode ser o resultado de um problema de gateway, uma autenticação por procuração em falta, um DNS incorreto ou uma falha de autenticação. Pode ver os seguintes erros:
   
   * ErrorCode.CiSApplianceGateway: Indica uma exceção HttpStatusCode.BadGateway: o serviço de resolução de nomes não conseguiu resolver o nome de anfitrião.
   * ErrorCode.CiSApplianceProxy: Indica uma exceção httpStatusCode.ProxyAuthenticationRequired (código de estado HTTP 407): o cliente não conseguiu autenticar-se com o servidor proxy.
   * ErrorCode.CiSApplianceDNSError: Indica uma exceção WebExceptionStatus.NameResolutionFailure: O serviço de resolução de nomes não conseguiu resolver o nome de anfitrião.
   * ErrorCode.CiSApplianceACSError: Indica que o serviço devolveu um erro de autenticação, mas existe conectividade.
     
     Se não lançar uma exceção web, verifique se o ErrorCode.CiSApplianceFailure, o que indica que o aparelho falhou.
5. Verifique a conectividade do serviço de nuvem. Se o serviço lançar uma exceção web, poderá ver os seguintes erros:
   
   * ErrorCode.CiSApplianceGateway: Indica uma exceção HttpStatusCode.BadGateway: um servidor de procuração intermédia recebeu um mau pedido de outro representante ou do servidor original.
   * ErrorCode.CiSApplianceProxy: Indica uma exceção httpStatusCode.ProxyAuthenticationRequired (código de estado HTTP 407): o cliente não conseguiu autenticar-se com o servidor proxy.
   * ErrorCode.CiSApplianceDNSError: Indica uma exceção WebExceptionStatus.NameResolutionFailure: o serviço de resolução de nome não conseguiu resolver o nome de anfitrião.
   * ErrorCode.CiSApplianceACSError: Indica que o serviço devolveu um erro de autenticação, mas existe conectividade.
     
     Se não lançar uma exceção web, verifique se errorCode.CiSApplianceSaasServiceError, o que indica um problema com o serviço StorSimple Device Manager.
6. Verifique a conectividade do Azure Service Bus. ErrorCode.CiSApplianceServiceBusError indica que o dispositivo não pode ligar-se ao Service Bus.

Os ficheiros de registo CiSCommandletLog0Curr.errlog e CiSAgentsvc0Curr.errlog terão mais informações, tais como detalhes de exceção.

Para obter mais informações sobre como utilizar o cmdlet, aceda ao [Test-HcsmConnection](/previous-versions/windows/powershell-scripting/dn715782(v=wps.630)) na documentação de referência do Windows PowerShell.

> [!IMPORTANT]
> Pode executar este cmdlet tanto para o controlador ativo como para o controlador passivo.

Consulte as seguintes amostras de saída do `Test-HcsmConnection` cmdlet.

**Saída da amostra – dispositivo registado com sucesso a executar StorSimple Update 3**

```output
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
```

**Saída da amostra – dispositivo offline** 

Esta amostra é de um dispositivo que tem um estado de **Offline** no portal Azure.

```output
Checking device registrationstate: Success
Device is registered successfully
Checking connectivity from device to SaaS.. Failure
```

O dispositivo não conseguiu ligar-se utilizando a configuração atual de procuração web. Pode haver um problema com a configuração de procuração web ou um problema de conectividade de rede. Neste caso, deve certificar-se de que as definições de procuração web estão corretas e que os seus servidores de procuração web estão online e contactáveis.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Resolução de problemas com o Sync-HcsTime cmdlet
Utilize este cmdlet para visualizar o tempo do dispositivo. Se o tempo do dispositivo tiver uma compensação com o servidor NTP, pode então utilizar este cmdlet para sincronizar o tempo com o servidor NTP.
- Se o offset entre o dispositivo e o servidor NTP for superior a 5 minutos, verá um aviso. 
- Se o offset exceder 15 minutos, o dispositivo ficará offline. Ainda pode usar este cmdlet para forçar uma sincronização temporal. 
- No entanto, se a compensação exceder 15 horas, então não será capaz de sincronizar a hora e será mostrada uma mensagem de erro.

**Saída da amostra - sincronização de tempo forçado usando Sync-HcsTime**

```output
Controller0>Sync-HcsTime
The current device time is 4/24/2015 4:05:40 PM UTC.

Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
[Y] Yes [N] No (Default is "Y"): Y
Controller0>
```

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Resolução de problemas com os Enable-HcsPing e Disable-HcsPing cmdlets
Utilize estes cmdlets para garantir que as interfaces de rede do seu dispositivo respondam aos pedidos de ping ICMP. Por predefinição, as interfaces de rede StorSimple não respondem aos pedidos de ping. A utilização deste cmdlet é a forma mais fácil de saber se o seu dispositivo está online e acessível.

**Saída da amostra - Enable-HcsPing e Desativador-HcsPing**

```output
Controller0>
Controller0>Enable-HcsPing
Successfully enabled ping.
Controller0>
Controller0>
Controller0>Disable-HcsPing
Successfully disabled ping.
Controller0>
```

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Resolução de problemas com o Trace-HcsRoute cmdlet
Utilize este cmdlet como ferramenta de rastreio de rotas. Envia pacotes para cada router a caminho de um destino final durante um período de tempo, e depois calcula os resultados com base nos pacotes devolvidos de cada salto. Como o cmdlet mostra o grau de perda de pacote em qualquer router ou link, pode localizar quais routers ou links podem estar a causar problemas de rede.

**Saída de amostra que mostra como rastrear a rota de um pacote com Trace-HcsRoute**

```output
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
```

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Resolução de problemas com o Get-HcsRoutingTable cmdlet
Utilize este cmdlet para visualizar a tabela de encaminhamento para o seu dispositivo StorSimple. Uma tabela de encaminhamento é um conjunto de regras que podem ajudar a determinar onde os pacotes de dados que viajam através de uma rede de Protocolo de Internet (IP) serão direcionados.

A tabela de encaminhamento mostra as interfaces e o gateway que encaminha os dados para as redes especificadas. Também dá a métrica de encaminhamento, que é o decisor para o caminho tomado para chegar a um determinado destino. Quanto mais baixa for a métrica de encaminhamento, maior a preferência.

Por exemplo, suponha que tem duas interfaces de rede, DATA 2 e DATA 3, ligadas à Internet. Se as métricas de encaminhamento para DATA 2 e DATA 3 forem 15 e 261 respectivamente, então o DATA 2, com a métrica de encaminhamento mais baixa, é a interface preferida utilizada para chegar à Internet.

Se estiver a executar a Atualização 1 no seu dispositivo StorSimple, a interface de rede DATA 0 tem a maior preferência pelo tráfego na nuvem. Com esta configuração, mesmo que existam outras interfaces ativadas pela nuvem, a maior parte do tráfego na nuvem seria encaminhado através do DATA 0.<!--This implies that even if there are other cloud-enabled interfaces, the cloud traffic would be routed through DATA 0. - Preceding sentence replaces this one. Use of "This implies" is a bit murky. DATA 0 will be the preferred network interface? Is my translation OK?-->

Se executar o `Get-HcsRoutingTable` cmdlet sem especificar quaisquer parâmetros (como mostra o exemplo a seguir), o cmdlet irá desausar as tabelas de encaminhamento IPv4 e IPv6. Em alternativa, pode especificar `Get-HcsRoutingTable -IPv4` ou obter uma tabela de `Get-HcsRoutingTable -IPv6`  encaminhamento relevante.

```output
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
```

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Passo a passo StorSimple exemplo de resolução de problemas
O exemplo a seguir mostra a resolução passo-a-passo de uma implantação StorSimple. No cenário de exemplo, o registo do dispositivo falha com uma mensagem de erro indicando que as definições de rede ou o nome DNS estão incorretos.

A mensagem de erro devolvida é:

```output
Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
+CategoryInfo: Not specified
+FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand
```

O erro pode ser causado por qualquer uma das seguintes questões:

* Instalação de hardware incorreta
* Interfaces de rede defeituosas
* Endereço IP incorreto, máscara de sub-rede, gateway, servidor primário de DNS ou procuração web
* Chave de registo incorreta
* Definições de firewall incorretas

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Para localizar e corrigir o problema de registo do dispositivo
1. Verifique a configuração do dispositivo: no controlador ativo, corra `Invoke-HcsSetupWizard` .
   
   > [!NOTE]
   > O assistente de configuração tem de funcionar no controlador ativo. Para verificar se está ligado ao controlador ativo, veja o banner apresentado na consola em série. O banner indica se está ligado ao controlador 0 ou ao controlador 1 e se o controlador está ativo ou passivo. Para mais informações, aceda à [identificação de um controlador ativo no seu dispositivo.](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)
   
2. Certifique-se de que o aparelho está corretamente ligado: verifique a cablagem de rede no plano traseiro do dispositivo. A cablagem é específica do modelo do dispositivo. Para mais informações, aceda à [instalação do seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instale o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Se estiver a utilizar portas de rede de 10 GbE, terá de utilizar os adaptadores QSFP-SFP e os cabos SFP fornecidos. Para obter mais informações, consulte a [lista de cabos, interruptores e transceptores recomendados para as portas de 10 GbE](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Verifique a saúde da interface de rede:
   
   * Utilize o cmdlet Get-NetAdapter para detetar a saúde das interfaces de rede para data 0. 
   * Se a ligação não estiver a funcionar, o `ifindex` estado indicará que a interface está em baixo. Em seguida, terá de verificar a ligação de rede da porta ao aparelho e ao interruptor. Também terá de excluir cabos estragados. 
   * Se suspeitar que a porta DATA 0 do controlador ativo falhou, pode confirmar que ligando a porta DATA 0 no controlador 1. Desligue o cabo de rede da parte de trás do dispositivo do controlador 0, ligue o cabo ao controlador 1 e, em seguida, volte a executar o Get-NetAdapter cmdlet.
   
     Se a porta DATA 0 de um controlador falhar, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para os próximos passos. Pode ser necessário substituir o controlador no seu sistema.
4. Verifique a conectividade com o interruptor:
   
   * Certifique-se de que as interfaces de rede DATA 0 no controlador 0 e no controlador 1 no seu compartimento primário estão na mesma sub-rede. 
   * Verifique o centro ou o router. Normalmente, deve ligar ambos os controladores ao mesmo hub ou router. 
   * Certifique-se de que os interruptores utilizados para a ligação têm DATA 0 para ambos os controladores no mesmo vLAN.
5. Eliminar quaisquer erros do utilizador:
   
   * Volte a executar o assistente de configuração (executar **Invoke-HcsSetupWizard),** e introduza novamente os valores para se certificar de que não existem erros. 
   * Verifique a chave de registo utilizada. A mesma chave de registo pode ser utilizada para ligar vários dispositivos a um serviço StorSimple Device Manager. Utilize o procedimento na [Obter a chave de registo de serviço](storsimple-8000-manage-service.md#get-the-service-registration-key) para garantir que está a utilizar a chave de registo correta.
     
     > [!IMPORTANT]
     > Se tiver vários serviços em execução, terá de garantir que a chave de registo do serviço adequado é utilizada para registar o dispositivo. Se registou um dispositivo com o serviço StorSimple Device Manager errado, terá de contactar o [Microsoft Support](storsimple-8000-contact-microsoft-support.md) para os próximos passos. Pode ter de efetuar um reset de fábrica do dispositivo (o que pode resultar na perda de dados) para, em seguida, ligá-lo ao serviço pretendido.
     > 
     > 
6. Utilize o Test-Connection cmdlet para verificar se tem conectividade com a rede externa. Para mais informações, aceda à [resolução de problemas com o Test-Connection cmdlet](#troubleshoot-with-the-test-connection-cmdlet).
7. Verifique se há interferência na firewall. Se verificou que as definições de IP virtual (VIP), sub-rede, gateway e DNS estão todas corretas, e ainda vê problemas de conectividade, é possível que a sua firewall esteja a bloquear a comunicação entre o seu dispositivo e a rede externa. Certifique-se de que as portas 80 e 443 estão disponíveis no seu dispositivo StorSimple para comunicação de saída. Para obter mais informações, consulte [os requisitos de networking do seu dispositivo StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Olhe para os troncos. Vá a [Pacotes de Suporte e registos de dispositivos disponíveis para resolução de problemas](#support-packages-and-device-logs-available-for-troubleshooting).
9. Se os passos anteriores não resolverem o problema, contacte o [Microsoft Support](storsimple-8000-contact-microsoft-support.md) para obter assistência.

## <a name="next-steps"></a>Passos seguintes
[Aprenda a utilizar a ferramenta De diagnóstico para resolver problemas com um dispositivo StorSimple](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd379547(v=ws.10)
[2]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd392266(v=ws.10)