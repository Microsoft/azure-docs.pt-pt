---
title: Resolver problemas de implementação de série 8000 do StorSimple | Documentos da Microsoft
description: Descreve como diagnosticar e corrigir os erros que ocorrem quando implantar primeiro o StorSimple.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64715211"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Resolver problemas de implementação de dispositivos do StorSimple
## <a name="overview"></a>Descrição geral
Este artigo fornece orientações de resolução de problemas úteis para a sua implementação do Microsoft Azure StorSimple. Ele descreve problemas comuns, as causas possíveis e passos recomendados para o ajudar a resolver problemas que podem ocorrer ao configurar o StorSimple. 

Estas informações aplicam-se para o dispositivo físico da série StorSimple 8000 e o StorSimple Cloud Appliance.

> [!NOTE]
> Problemas relacionados com a configuração de dispositivos que pode enfrentar podem ocorrer quando implementar o dispositivo pela primeira vez ou podem ocorrer mais tarde, quando o dispositivo está operacional. Este artigo se concentra na resolução de problemas de implementação de iniciantes. Para resolver problemas relacionados com um dispositivo operacional, aceda a [utilizar a ferramenta diagnósticos para resolver problemas de um dispositivo operacional](storsimple-8000-diagnostics.md).

Este artigo também descreve as ferramentas para resolver problemas de implementações do StorSimple e fornece um exemplo de resolução de problemas passo a passo.

## <a name="first-time-deployment-issues"></a>Problemas de implantação de iniciantes
Caso se depare com um problema ao implementar o seu dispositivo pela primeira vez, considere o seguinte:

* Se estiver a resolver um dispositivo físico, certifique-se de que o hardware foi instalado e configurado conforme descrito em [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
* Verifique os pré-requisitos para a implementação. Certifique-se de que tem todas as informações descritas a [lista de verificação de configuração de implementação](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Reveja as notas de versão do StorSimple para ver se o problema é descrito. As notas de versão incluem soluções para problemas conhecidos. 

Durante a implementação do dispositivo, o mais comum problemas que os utilizadores face ocorrem quando executam o Assistente de configuração e quando eles registem o dispositivo através do Windows PowerShell para StorSimple. (Utilizar Windows PowerShell para StorSimple para se registrar e configurar o dispositivo StorSimple. Para obter mais informações sobre o registo de dispositivos, consulte [passo 3: Configurar e registar o seu dispositivo através do Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

As secções seguintes podem ajudar a resolver problemas que encontrar quando configurar o dispositivo StorSimple pela primeira vez.

## <a name="first-time-setup-wizard-process"></a>Processo do Assistente de configuração de iniciantes
Os passos seguintes resumem o processo do Assistente de configuração. Para informações de instalação detalhadas, consulte [implementar no seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md).

1. Executar o [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) cmdlet para iniciar o Assistente de configuração que irá orientá-lo os restantes passos. 
2. Configurar a rede: o Assistente de configuração permite-lhe configurar definições de rede para a interface de rede 0 de dados no dispositivo StorSimple. Estas definições incluem o seguinte:
   * Virtual IP (VIP), máscara de sub-rede e gateway – a [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) cmdlet é executado em segundo plano. Configura o endereço IP, a máscara de sub-rede e o gateway para a interface de rede 0 de dados no dispositivo StorSimple.
   * Servidor DNS principal – a [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) cmdlet é executado em segundo plano. Configura as definições de DNS para a sua solução StorSimple.
   * O servidor NTP – a [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet é executado em segundo plano. Configura as definições do servidor NTP para a sua solução StorSimple.
   * Proxy de web opcional – o [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) cmdlet é executado em segundo plano. Ele define e permite a configuração de proxy web para a sua solução StorSimple.
3. Configurar a palavra-passe: a próxima etapa é configurar a palavra-passe de administrador do dispositivo.
   A palavra-passe de administrador do dispositivo é utilizada para iniciar sessão no seu dispositivo. A palavra-passe predefinida do dispositivo é **Password1**.
        
     > [!IMPORTANT]
     > As palavras-passe são recolhidas antes do registo, mas aplicadas apenas depois de registar o dispositivo com êxito. Se houver uma falha ao aplicar uma palavra-passe, será solicitado que forneça a palavra-passe novamente até que as palavras-passe necessárias (que cumprem os requisitos de complexidade) são recolhidas.
     
4. Registar o dispositivo: a etapa final é ao registar o dispositivo com o serviço StorSimple Device Manager em execução no Microsoft Azure. O registo requer que [obter a chave de registo do serviço](storsimple-8000-manage-service.md#get-the-service-registration-key) do portal do Azure e fornecê-lo no Assistente de configuração. **Depois do dispositivo está registado com êxito, é fornecida uma chave de encriptação de dados do serviço para. Certifique-se de que guarde esta chave de encriptação numa localização segura, porque é necessária para registar todos os dispositivos posteriores com o serviço.**

## <a name="common-errors-during-device-deployment"></a>Erros comuns durante a implementação do dispositivo
As tabelas seguintes listam os erros comuns que poderão surgir quando:

* Configure as definições de rede necessária.
* Configure as definições de proxy web opcional.
* Configure a palavra-passe de administrador do dispositivo.
* Registe o dispositivo.

## <a name="errors-during-the-required-network-settings"></a>Erros durante as definições de rede necessária
| Não. | Mensagem de erro | Causas possíveis | Ação recomendada |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Este comando só pode ser executado no controlador ativo. |Configuração estava a ser efetuada no controlador passivo. |Execute este comando a partir do controlador ativo. Para obter mais informações, consulte [identificar um controlador ativo no seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: Dispositivo não está pronto. |Existem problemas com a conectividade de rede DATA 0. |Verifique a conectividade de rede física no DATA 0. |
| 3 |Invoke-HcsSetupWizard: Existe um conflito de endereços IP com outro sistema na rede (exceção de HRESULT: 0x80070263). |O IP fornecido para dados 0 já foi utilizado por outro sistema. |Forneça um novo IP não está em utilização. |
| 4 |Invoke-HcsSetupWizard: Falha de um recurso de cluster. (Exceção de HRESULT: 0x800713AE). |VIP duplicado. O IP fornecido já está em utilização. |Forneça um novo IP não está em utilização. |
| 5 |Invoke-HcsSetupWizard: Endereço IPv4 inválido. |O endereço IP é fornecido num formato incorreto. |Verifique o formato e forneça o seu endereço IP novamente. Para obter mais informações, consulte [endereçamento Ipv4][1]. |
| 6 |Invoke-HcsSetupWizard: Endereço de IPv6 inválido. |O endereço IP é fornecido num formato incorreto. |Verifique o formato e forneça o seu endereço IP novamente. Para obter mais informações, consulte [endereçamento Ipv6][2]. |
| 7 |Invoke-HcsSetupWizard: Não existem mais pontos finais estão disponíveis no mapeador. (Exceção de HRESULT: 0x800706D9) |A funcionalidade de cluster não está a funcionar. |[Contacte o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para saber quais os próximos passos. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Erros durante as definições de proxy web opcional
| Não. | Mensagem de erro | Causas possíveis | Ação recomendada |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Parâmetro inválido (exceção de HRESULT: 0x80070057) |Um dos parâmetros fornecidos para que as definições de proxy não é válido. |O URI não é fornecido no formato correto. Utilize o seguinte formato: http:// *\<IP endereço ou FQDN do servidor de proxy web >* : *\<número da porta TCP >* |
| 2 |Invoke-HcsSetupWizard: Servidor RPC não está disponível (exceção de HRESULT: 0x800706ba) |A causa raiz é um dos seguintes:<ol><li>O cluster não é até.</li><li>O controlador passivo não consegue comunicar com o controlador ativo e o comando é executado a partir do controlador passivo.</li></ol> |Dependendo da causa raiz:<ol><li>[Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para se certificar de que o cluster está ativo.</li><li>Execute o comando a partir do controlador ativo. Se quiser executar o comando de controlador passivo, terá de se certificar de que o controlador passivo pode comunicar com o controlador ativo. Precisará [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) se essa conectividade está quebrada.</li></ol> |
| 3 |Invoke-HcsSetupWizard: Falha na chamada RPC (exceção de HRESULT: 0x800706be) |Cluster está inativo. |[Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para se certificar de que o cluster está ativo. |
| 4 |Invoke-HcsSetupWizard: Não foram encontrados recursos de cluster (exceção de HRESULT: 0x8007138f) |O recurso de cluster não foi encontrado. Isto pode acontecer quando a instalação não estava correta. |Poderá ter de repor o dispositivo para as predefinições de fábrica. [Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para criar um recurso de cluster. |
| 5 |Invoke-HcsSetupWizard: Recurso não online de cluster (exceção de HRESULT: 0x8007138c) |Recursos do cluster não estão online. |[Contacte o Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md) para saber quais os próximos passos. |

## <a name="errors-related-to-device-administrator-password"></a>Erros relacionados com a palavra-passe de administrador do dispositivo
A palavra-passe de administrador do dispositivo de predefinido é **Password1**. Esta palavra-passe expira após o primeiro início de sessão; Por conseguinte, terá de utilizar o Assistente de configuração para alterá-la. Tem de fornecer uma nova senha de administrador do dispositivo quando registar o dispositivo pela primeira vez. 

Certifique-se de que as palavras-passe cumpram os seguintes requisitos:

* A palavra-passe de administrador do dispositivo deve ter entre 8 e 15 carateres de comprimento.
* As senhas devem conter 3 dos seguintes tipos de 4 carateres: em minúsculas, letras maiúsculas, numéricos e especiais. 
* A palavra-passe não pode ser o mesmo que as últimas 24 palavras-passe.

Além disso, tenha em atenção que as palavras-passe expirarem todos os anos e podem ser alteradas apenas depois de registar o dispositivo com êxito. Se o registo falhar por algum motivo, as palavras-passe não serão alteradas.

Para obter mais informações sobre a palavra-passe de administrador do dispositivo, aceda a [utilizar o serviço StorSimple Device Manager para alterar a palavra-passe do StorSimple](storsimple-8000-change-passwords.md).

Pode encontrar um ou mais dos seguintes erros ao configurar o administrador do dispositivo e as palavras-passe do Snapshot Manager do StorSimple.

| Não. | Mensagem de erro | Ação recomendada |
| --- | --- | --- |
| 1 |A palavra-passe excede o comprimento máximo. |A palavra-passe de administrador do dispositivo tem de ter entre 8 e 15 carateres de comprimento. |
| 2 |A palavra-passe não cumpre o comprimento necessário. |A palavra-passe de administrador do dispositivo tem de ter entre 8 e 15 carateres de comprimento.|
| 3 |A palavra-passe tem de conter carateres em minúsculas. |As palavras-passe tem de conter 3 dos seguintes tipos de 4 carateres: em minúsculas, letras maiúsculas, numéricos e especiais. Certifique-se de que a palavra-passe cumpre estes requisitos. |
| 4 |A palavra-passe tem de conter carateres numéricos. |As palavras-passe tem de conter 3 dos seguintes tipos de 4 carateres: em minúsculas, letras maiúsculas, numéricos e especiais. Certifique-se de que a palavra-passe cumpre estes requisitos. |
| 5 |A palavra-passe tem de conter carateres especiais. |As palavras-passe tem de conter 3 dos seguintes tipos de 4 carateres: em minúsculas, letras maiúsculas, numéricos e especiais. Certifique-se de que a palavra-passe cumpre estes requisitos. |
| 6 |A palavra-passe tem de conter 3 dos seguintes tipos de 4 carateres: letras maiúsculas, minúsculas, numéricos e especiais. |A palavra-passe não contém os tipos necessários de carateres. Certifique-se de que a palavra-passe cumpre estes requisitos. |
| 7 |Parâmetro não corresponde a confirmação. |Certifique-se de que a palavra-passe cumpre todos os requisitos e que introduziu corretamente. |
| 8 |A palavra-passe não pode corresponder a predefinição. |A palavra-passe predefinido é *Password1*. Terá de alterar esta palavra-passe depois de iniciar sessão pela primeira vez. |
| 9 |A palavra-passe que introduziu não corresponde a palavra-passe do dispositivo. . Escreva novamente a palavra-passe. |Verifique a palavra-passe e escrevê-lo novamente. |

As palavras-passe são recolhidas antes do dispositivo está registado, mas são aplicadas apenas após o registo com êxito. O fluxo de trabalho de recuperação de palavra-passe requer que o dispositivo ser registado.

> [!IMPORTANT]
> Em geral, se uma tentativa para aplicar uma palavra-passe falhar, em seguida, o software repetidamente tenta recolher a palavra-passe até ter êxito. Em situações raras, não é possível aplicar a palavra-passe. Nesta situação, pode registar o dispositivo e continuar, no entanto não serão possível alterar as palavras-passe. Pode alterar a palavra-passe de administrador do dispositivo após o registo do portal do Azure.


Pode repor a palavra-passe no portal do Azure através do serviço StorSimple Device Manager. Para obter mais informações, aceda a [alterar a palavra-passe de administrador do dispositivo](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Erros durante o registo do dispositivo
Utilizar o serviço StorSimple Device Manager em execução no Microsoft Azure para registar o dispositivo. É possível encontrar um ou mais dos seguintes problemas durante o registo do dispositivo.

| Não. | Mensagem de erro | Causas possíveis | Ação recomendada |
| --- | --- | --- | --- |
| 1 |Erro 350027: Falha ao registar o dispositivo com o Gestor de dispositivos do StorSimple. | |Aguarde alguns minutos e, em seguida, repita a operação. Se o problema persistir, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 2 |Erro 350013: Erro ao registar o dispositivo. Isto pode dever-se a chave de registo do serviço incorreta. | |Registe novamente o dispositivo com a chave de registo do serviço correta. Para obter mais informações, consulte [obter a chave de registo do serviço.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Erro 350063: Falha na autenticação para o serviço StorSimple Device Manager passado, mas o registo. Repita a operação após algum tempo. |Este erro indica que passou a autenticação com o ACS, mas a chamada de registro feita para o serviço falhou. Isto pode ser um resultado de uma falha de rede esporadicamente. |Se o problema persistir, tente [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 4 |Erro 350049: Não foi possível contactar com o serviço durante o registo. |Quando a chamada é feita para o serviço, uma exceção da web é recebida. Em alguns casos, isso poderá obter corrigido ao repetir a operação mais tarde. |Verifique o endereço IP e o nome DNS e, em seguida, repita a operação. Se o problema persistir, [contacte Support da Microsoft.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Erro 350031: O dispositivo já foi registado. | |Nenhuma ação necessária. |
| 6 |Erro 350016: Falha no registo de dispositivo. | |Certifique-se de que a chave de registo está correta. |
| 7 |Invoke-HcsSetupWizard: Ocorreu um erro ao registar o seu dispositivo; Isto pode dever-se a endereço IP incorreto ou o nome DNS. Verifique as definições de rede e tente novamente. Se o problema persistir, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md). (Erro 350050) |Certifique-se de que o dispositivo pode enviar um ping à rede externa. Se não tiver conectividade à rede externa, o registo pode falhar com este erro. Este erro pode ser uma combinação de um ou mais dos seguintes procedimentos:<ul><li>IP incorreto</li><li>Sub-rede incorreta</li><li>Gateway incorreto</li><li>Definições de DNS incorretas</li></ul> |Veja os passos a [exemplo de resolução de problemas passo a passo](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: A operação atual falhou devido a um erro de serviço interno [0x1FBE2]. Repita a operação após algum tempo. Se o problema persistir, contacte o Support da Microsoft. |Este é um erro genérico lançado para todos os erros de invisível de utilizador do serviço ou o agente. A razão mais comum é possível que a autenticação de ACS falhou. Uma causa possível para a falha é que existem problemas com a configuração do servidor NTP e hora no dispositivo não está definida corretamente. |Corrija a hora (se existirem problemas) e, em seguida, repita a operação de registo. Se utilizar o comando Set-HcsSystem - Timezone para ajustar o fuso horário, em maiúsculas cada palavra no fuso horário (por exemplo "Hora padrão do Pacífico").  Se este problema persistir, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para passos seguintes. |
| 9 |Aviso: Não foi possível ativar o dispositivo. O administrador de dispositivo e as palavras-passe do Snapshot Manager do StorSimple não foram alteradas. |Se o registo falhar, o administrador do dispositivo e as palavras-passe do Snapshot Manager do StorSimple não são alteradas. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Ferramentas para resolver problemas de implementações do StorSimple
StorSimple inclui várias ferramentas que pode utilizar para resolver problemas relacionados com a solução StorSimple. Estas incluem:

* Suportar pacotes e registos de dispositivos.
* Cmdlets concebidos especificamente para resolução de problemas.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Suportar os pacotes e registos de dispositivo disponíveis para resolução de problemas
Um pacote de suporte contém todos os registos relevantes que podem auxiliar a equipe de Support da Microsoft com a resolução de problemas do dispositivo. Pode usar o Windows PowerShell para StorSimple para gerar um pacote de suporte encriptados que, em seguida, pode partilhar com suporte técnico.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Para ver os registos ou o conteúdo do pacote de suporte
1. Utilizar o Windows PowerShell para StorSimple para gerar um pacote de suporte, conforme descrito em [criar e gerir um pacote de suporte](storsimple-8000-create-manage-support-package.md).
2. Transfira o [script de desencriptação](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente no computador cliente.
3. Utilize esta opção [procedimento passo a passo](storsimple-8000-create-manage-support-package.md#edit-a-support-package) para abrir e descriptografar o pacote de suporte.
4. Os registos de pacote de suporte desencriptada estão no formato de etw/etvx. Pode executar os passos seguintes para ver estes ficheiros no Visualizador de eventos do Windows:
   
   1. Executar o **eventvwr** comando no cliente Windows. Isso iniciará o Visualizador de eventos.
   2. Na **ações** painel, clique em **registo guardado abertos** e aponte para os ficheiros de registo no formato de etvx/etw (o pacote de suporte). Agora, pode ver o ficheiro. Depois de abrir o ficheiro, pode com o botão direito e salve o arquivo como texto.
      
      > [!IMPORTANT]
      > Também pode utilizar o **Get-WinEvent** cmdlet para abrir estes ficheiros no Windows PowerShell. Para obter mais informações, consulte [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) na documentação de referência de cmdlet do Windows PowerShell.
     
5. Quando abrir os registos no Visualizador de eventos, procure os seguintes registos que contêm problemas relacionados com a configuração do dispositivo:
   
   * hcs_pfconfig/Operational registo
   * hcs_pfconfig/Config
6. Nos ficheiros de registo, procura cadeias de caracteres relacionadas com os cmdlets chamados pelo Assistente de configuração. Ver [processo do Assistente de configuração de iniciantes](#first-time-setup-wizard-process) para obter uma lista destes cmdlets.
7. Se não for capaz de descobrir a causa do problema, pode [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para passos seguintes. Utilize os passos em [criar um pedido de suporte](storsimple-8000-contact-microsoft-support.md#create-a-support-request) quando contactar o Support da Microsoft para obter assistência.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlets para resolução de problemas
Utilize os seguintes cmdlets Windows PowerShell para detectar erros de conectividade.

* `Get-NetAdapter`: Utilize este cmdlet para detetar o estado de funcionamento de interfaces de rede.
* `Test-Connection`: Utilize este cmdlet para verificar a conectividade de rede dentro e fora da rede.
* `Test-HcsmConnection`: Utilize este cmdlet para verificar a conectividade de um dispositivo registado com êxito.
* `Sync-HcsTime`: Utilize este cmdlet para apresentar a hora do dispositivo e forçar a sincronização de hora com o servidor NTP.
* `Enable-HcsPing` e `Disable-HcsPing`: Utilize estes cmdlets para permitir que os anfitriões a enviar pings para as interfaces de rede no seu dispositivo StorSimple. Por predefinição, as interfaces de rede do StorSimple não responda a pedidos de ping.
* `Trace-HcsRoute`: Utilize este cmdlet como uma ferramenta de rastreamento de rota. Envia pacotes para cada router a caminho para um destino final durante um período de tempo e, em seguida, calcula os resultados com base nos pacotes devolvidos de cada salto. Uma vez que `Trace-HcsRoute` mostra o grau de perda de pacotes em qualquer determinado router ou uma ligação, pode identificar quais routers ou ligações poderão estar a causar problemas de rede.
* `Get-HcsRoutingTable`: Utilize este cmdlet para exibir a tabela de encaminhamento de IP local.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Resolver problemas com o cmdlet Get-NetAdapter
Ao configurar interfaces de rede para uma implantação no dispositivo de iniciantes, o estado de hardware não está disponível no serviço StorSimple Device Manager da interface do Usuário porque o dispositivo ainda não está registado com o serviço. Além disso, o **estado de funcionamento do Hardware** painel pode não refletir sempre corretamente o estado do dispositivo, especialmente se existirem problemas que afetam a sincronização de serviço. Nestas situações, pode usar o `Get-NetAdapter` cmdlet para determinar o estado de funcionamento e o estado das suas interfaces de rede.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Para ver uma lista de todos os adaptadores de rede no seu dispositivo
1. Iniciar o Windows PowerShell para StorSimple e, em seguida, escreva `Get-NetAdapter`. 
2. Utilizar o resultado do `Get-NetAdapter` cmdlet e as diretrizes seguintes para compreender o estado da sua interface de rede.
   
   * Se a interface é bom estado de funcionamento e ativada, o **ifIndex** status é mostrado como **cópia**.
   * Se a interface está em bom estada, mas não é fisicamente ligada (através de um cabo de rede), o **ifIndex** é mostrado como **desativado**.
   * Se a interface é bom estado de funcionamento, mas não ativado, o **ifIndex** status é mostrado como **NotPresent**.
   * Se a interface não existir, ele não aparece nesta lista. O serviço StorSimple Device Manager da interface do Usuário ainda irá mostrar essa interface em estado de falha.

Para obter mais informações sobre como utilizar este cmdlet, aceda a [Get-NetAdapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps) na referência de cmdlets do Windows PowerShell.

As secções seguintes mostram exemplos de saída a partir do `Get-NetAdapter` cmdlet.

 Estes exemplos, o controlador 0 foi o controlador passivo e foi configurado da seguinte forma:

* DATA 0, 1 de dados, dados 2 e interfaces existiam no dispositivo de rede de dados 3.
* DADOS 4 e 5 de dados de placas de interface de rede não estavam presentes; Por conseguinte, não estão listadas na saída.
* DATA 0 foi ativada.

O controlador 1 foi o controlador ativo e tiver sido configurado da seguinte forma:

* DADOS 0, 1 de dados, 2 de dados, dados 3, 4 de dados e interfaces existiam no dispositivo de rede de 5 de dados.
* DATA 0 foi ativada.

**Saída de exemplo – controlador 0**

Segue-se a saída do controlador 0 (o controlador passivo). DADOS 1, 2 de dados e dados 3 não estão ligados. DADOS 4 e 5 de dados não estão listadas porque não estão presentes no dispositivo.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Saída de exemplo – controlador 1**

Segue-se a saída do controlador 1 (o controlador ativo). Apenas os dados 0 é configurada a interface de rede no dispositivo e trabalho.

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


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Resolver problemas com o cmdlet Test-Connection
Pode utilizar o `Test-Connection` cmdlet para determinar se o dispositivo StorSimple pode ligar-se à rede externa. Se todos os parâmetros de rede, incluindo o DNS estão configurados corretamente no Assistente de configuração, pode utilizar o `Test-Connection` cmdlet para efetuar o ping num endereço conhecido fora da rede, como o outlook.com.

Deve habilitar o ping resolver problemas de conectividade com este cmdlet, se o ping está desativada.

Consulte os seguintes exemplos de saída a partir do `Test-Connection` cmdlet.

> [!NOTE]
> No primeiro exemplo, o dispositivo está configurado com um DNS incorreto. No segundo exemplo, o DNS está correto.

**Saída de exemplo – um DNS incorreto**

No exemplo a seguir, não há nenhuma saída para os endereços IPV4 e IPV6, que indica que o DNS não for resolvido. Isso significa que nenhuma conectividade à rede externa e necessita de fornecer um DNS correto.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Saída de exemplo – DNS correto**

No exemplo a seguir, o DNS retorna o endereço IPV4, que indica que o DNS está configurado corretamente. Isto confirma que existe conectividade à rede externa.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Resolver problemas com o cmdlet Test-HcsmConnection
Utilize o `Test-HcsmConnection` cmdlet para um dispositivo que já está ligado à e registado com o seu serviço StorSimple Device Manager. Este cmdlet ajuda-o a verificar a conectividade entre um dispositivo registado e o serviço StorSimple Device Manager correspondente. Pode executar este comando no Windows PowerShell para StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Para executar o cmdlet Test-HcsmConnection
1. Certifique-se de que o dispositivo está registado.
2. Verificar o estado do dispositivo. Se o dispositivo é desativado, no modo de manutenção ou offline, poderá ver um dos seguintes erros:
   
   * ErrorCode.CiSDeviceDecommissioned – isso indica que o dispositivo é desativado.
   * ErrorCode.DeviceNotReady – isso indica que o dispositivo estiver no modo de manutenção.
   * ErrorCode.DeviceNotReady – isso indica que o dispositivo não está online.
3. Certifique-se de que o serviço StorSimple Device Manager está em execução (utilizar o [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) cmdlet). Se o serviço de mensagens em fila não está em execução, poderá ver os seguintes erros:
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – isso indica que ocorreu uma exceção quando executou o Get-ClusterResource.
4. Verifique o token do serviço de controlo de acesso (ACS). Se ele lançará uma exceção da web, poderá ser o resultado de um problema de gateway, uma autenticação de proxy em falta, um DNS incorreto ou uma falha de autenticação. Poderá ver os seguintes erros:
   
   * ErrorCode.CiSApplianceGateway – Isto indica que uma exceção de HttpStatusCode.BadGateway: o serviço de resolução de nome não foi possível resolver o nome do anfitrião.
   * ErrorCode.CiSApplianceProxy – Isto indica que uma exceção de HttpStatusCode.ProxyAuthenticationRequired (código de estado HTTP 407): o cliente não foi possível autenticar com o servidor proxy.
   * ErrorCode.CiSApplianceDNSError – Isto indica que uma exceção de WebExceptionStatus.NameResolutionFailure: o serviço de resolução de nome não foi possível resolver o nome do anfitrião.
   * ErrorCode.CiSApplianceACSError – isso indica que o serviço devolveu um erro de autenticação, mas não existe conectividade.
     
     Se não lança uma exceção da web, verifique se existem ErrorCode.CiSApplianceFailure. Isto indica que a aplicação falhou.
5. Verifique a conectividade do serviço de nuvem. Se o serviço gera uma exceção de web, poderá ver os seguintes erros:
   
   * ErrorCode.CiSApplianceGateway – Isto indica que uma exceção de HttpStatusCode.BadGateway: um servidor de proxy intermediário recebeu um pedido incorreto do outro proxy ou do servidor original.
   * ErrorCode.CiSApplianceProxy – Isto indica que uma exceção de HttpStatusCode.ProxyAuthenticationRequired (código de estado HTTP 407): o cliente não foi possível autenticar com o servidor proxy.
   * ErrorCode.CiSApplianceDNSError – Isto indica que uma exceção de WebExceptionStatus.NameResolutionFailure: o serviço de resolução de nome não foi possível resolver o nome do anfitrião.
   * ErrorCode.CiSApplianceACSError – isso indica que o serviço devolveu um erro de autenticação, mas não existe conectividade.
     
     Se não lança uma exceção da web, verifique se existem ErrorCode.CiSApplianceSaasServiceError. Isto indica um problema com o serviço StorSimple Device Manager.
6. Verifique a conectividade do Azure Service Bus. ErrorCode.CiSApplianceServiceBusError indica que o dispositivo não é possível ligar para o Service Bus.

O registo de ficheiros CiSCommandletLog0Curr.errlog e CiSAgentsvc0Curr.errlog terá mais informações, como detalhes da exceção.

Para obter mais informações sobre como utilizar o cmdlet, aceda a [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) no Windows PowerShell documentação de referência.

> [!IMPORTANT]
> Pode executar este cmdlet para o Active Directory e o controlador passivo.

Consulte os seguintes exemplos de saída a partir do `Test-HcsmConnection` cmdlet.

**Saída de exemplo – dispositivo registado com êxito a executar a atualização 3 do StorSimple**

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

**Saída de exemplo – dispositivo offline** 

Este exemplo é a partir de um dispositivo que tem o estado **Offline** no portal do Azure.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

Não foi possível ligar o dispositivo com a configuração de proxy web atual. Isto pode ser um problema com a configuração de proxy da web ou um problema de conectividade de rede. Neste caso, deve fazer-se de que as definições de proxy web estão corretas e seus servidores proxy web estão online e acessível.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Resolver problemas com o cmdlet Sync-HcsTime
Utilize este cmdlet para apresentar a hora do dispositivo. Se a hora do dispositivo tem um deslocamento com o servidor NTP, em seguida, pode utilizar este cmdlet força-sincronizar a hora com o seu servidor NTP.
- Se o deslocamento entre o dispositivo e o servidor NTP for superior a 5 minutos, verá um aviso. 
- Se o deslocamento exceder 15 minutos, em seguida, o dispositivo irá ficar offline. Ainda pode utilizar este cmdlet para forçar a sincronização de hora. 
- No entanto, se o deslocamento exceder 15 horas, em seguida, não será capaz de sincronização de força a hora e uma mensagem de erro serão exibidas.

**Saída de exemplo – sincronização de hora forçado com o Sync-HcsTime**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Resolver problemas com os cmdlets Enable HcsPing e Disable-HcsPing
Utilize estes cmdlets para garantir que as interfaces de rede no seu dispositivo respondem a pedidos de ping ICMP. Por predefinição, as interfaces de rede do StorSimple não responda a pedidos de ping. Utilizar este cmdlet é a maneira mais fácil de saber se o seu dispositivo está online e acessível.

**Saída de exemplo – Enable HcsPing e Disable-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Resolver problemas com o cmdlet HcsRoute de rastreio
Utilize este cmdlet como uma ferramenta de rastreamento de rota. Envia pacotes para cada router a caminho para um destino final durante um período de tempo e, em seguida, calcula os resultados com base nos pacotes devolvidos de cada salto. Uma vez que o cmdlet mostra o grau de perda de pacotes em qualquer determinado router ou uma ligação, pode identificar quais routers ou ligações poderão estar a causar problemas de rede.

**Saída de exemplo que mostra como rastrear a rota de um pacote com HcsRoute de rastreio**

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

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Resolver problemas com o cmdlet Get-HcsRoutingTable
Utilize este cmdlet para ver a tabela de encaminhamento para o dispositivo StorSimple. Uma tabela de encaminhamento é um conjunto de regras que podem ajudar a determinar onde serão direcionados pacotes de dados em trânsito através de uma rede de Internet Protocol (IP).

A tabela de roteamento mostra as interfaces e o gateway que encaminha os dados para redes especificadas. Isso também permite a métrica de encaminhamento que é o tomador de decisões para o caminho percorrido de acessar um destino específico. Menor a métrica de encaminhamento, maior será a preferência.

Por exemplo, se tiver 2 interfaces de rede, dados 2 e dados 3, ligado à Internet. Se as métricas de encaminhamento de dados 2 e 3 de dados são 15 e 261 respectivamente, em seguida, 2 de dados com a métrica mais baixa de encaminhamento é a interface preferencial utilizada para a Internet.

Se estiver a executar o Update 1 no dispositivo StorSimple, sua interface de rede 0 de dados tem a preferência mais elevada para o tráfego de nuvem. Isso implica que, mesmo se existirem outras interfaces de capacidade de cloud, o tráfego de nuvem seria encaminhado através de dados 0.

Se executar o `Get-HcsRoutingTable` cmdlet sem especificar quaisquer parâmetros (como mostrado no seguinte exemplo), o cmdlet irá transmitir a tabelas de roteamento IPv4 e IPv6. Em alternativa, pode especificar `Get-HcsRoutingTable -IPv4` ou `Get-HcsRoutingTable -IPv6` para obter uma tabela de roteamento relevante.

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

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Exemplo de resolução de problemas de StorSimple passo a passo
O exemplo seguinte mostra a resolução de problemas passo a passo de uma implementação do StorSimple. O cenário de exemplo, o registo do dispositivo falha com uma mensagem de erro indicando que as definições de rede ou o nome DNS está incorreto.

A mensagem de erro devolvida é:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

O erro pode ser causado por qualquer um dos seguintes:

* Instalação de hardware incorreto
* Interface de rede com falhas (s)
* Endereço IP incorreto, máscara de sub-rede, gateway, servidor DNS primário ou web proxy
* Chave de registo incorreto
* Definições da firewall incorreta

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Para localizar e corrigir o problema de registo do dispositivo
1. Verifique a configuração do dispositivo: no controlador ativo, executar `Invoke-HcsSetupWizard`.
   
   > [!NOTE]
   > O Assistente de configuração tem de executar no controlador ativo. Para verificar que está ligado ao controlador ativo, observe a faixa apresentada na consola de série do. A faixa indica se está ligado ao controlador 0 ou 1 do controlador e, se o controlador é ativas ou passivas. Para obter mais informações, aceda a [identificar um controlador ativo no seu dispositivo](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Certifique-se de que o dispositivo é instalou os cabos corretamente: Verifique a rede cablagem no dispositivo volta planos. A cablagem é específica para o modelo do dispositivo. Para obter mais informações, aceda a [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Se estiver a utilizar as portas de rede de 10 GbE, terá de utilizar os adaptadores de QSFP SFP fornecidos e cabos SFP. Para obter mais informações, consulte a [lista de cabos, comutadores e transcetores recomendados para as portas de GbE 10](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Verificar o estado de funcionamento da interface de rede:
   
   * Utilize o cmdlet Get-NetAdapter para detetar o estado de funcionamento das interfaces de rede para os dados 0. 
   * Se a ligação não está a funcionar, o **ifindex** estado indicará que a interface está desativado. Em seguida, terá de verificar a ligação de rede da porta para a aplicação e ao comutador. Também terá de eliminar os cabos ruins. 
   * Se suspeitar de que os dados 0 falha na porta no controlador ativo, pode confirmar isto conectando-se aos dados porta 0 no controlador 1. Para confirmar, desligue o cabo de rede da parte de trás do dispositivo do controlador 0, ligue o cabo do controlador 1 e, em seguida, execute o cmdlet Get-NetAdapter novamente.
     Se os dados 0 de porta num controlador de falha, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para passos seguintes. Poderá ter de substituir o controlador no seu sistema.
4. Verificar a conectividade ao comutador:
   
   * Certifique-se de que as interfaces de rede 0 de dados no controlador 0 e 1 do controlador no seu bastidor principal estão na mesma sub-rede. 
   * Verifique o hub ou do roteador. Normalmente, deve se conectar os dois controladores no mesmo hub ou router. 
   * Certifique-se de que os comutadores que utiliza para a ligação tem dados 0 para ambos os controladores na mesma vLAN.
5. Elimine quaisquer erros de utilizador:
   
   * Execute novamente o Assistente de configuração (execute **Invoke-HcsSetupWizard**) e introduza os valores novamente para certificar-se de que não há nenhum erro. 
   * Verifique se o registo utilizado de chave. A mesma chave de registo pode ser utilizada para ligar vários dispositivos para um serviço StorSimple Device Manager. Utilize o procedimento [obter a chave de registo do serviço](storsimple-8000-manage-service.md#get-the-service-registration-key) para se certificar de que está a utilizar a chave de registo correto.
     
     > [!IMPORTANT]
     > Se tiver vários serviços em execução, terá de se certificar de que a chave de registo para o serviço apropriado é utilizada para registar o dispositivo. Se registou um dispositivo com o serviço StorSimple Device Manager errada, precisará [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para passos seguintes. Poderá ter de efetuar uma reposição de fábrica do dispositivo (o que pode resultar em perda de dados) para, em seguida, ligá-la para o serviço pretendido.
     > 
     > 
6. Utilize o cmdlet Test-Connection para verificar se tem conectividade à rede externa. Para obter mais informações, aceda a [resolver problemas com o cmdlet Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Verificar a existência de interferência de firewall. Se tiver verificado que as definições de IP (VIP), sub-rede, gateway e DNS virtual estão corretas e continuar a ver problemas de conectividade, em seguida, é possível que a firewall está a bloquear a comunicação entre o dispositivo e da rede externa. Precisa garantir que as portas 80 e 443 estão disponíveis no dispositivo StorSimple para comunicações de saída. Para obter mais informações, consulte [requisitos de rede para o dispositivo StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Consulte os registos. Aceda a [suportar os pacotes e registos de dispositivo disponíveis para resolução de problemas](#support-packages-and-device-logs-available-for-troubleshooting).
9. Se os passos anteriores não resolverem o problema, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para obter assistência.

## <a name="next-steps"></a>Passos Seguintes
[Saiba como utilizar a ferramenta diagnósticos para resolver problemas de um dispositivo StorSimple](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
