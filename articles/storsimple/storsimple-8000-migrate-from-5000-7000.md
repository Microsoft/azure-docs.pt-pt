---
title: Migrar os dados no StorSimple 5000-7000 série para o dispositivo da 8000 série | Documentos da Microsoft
description: Fornece uma descrição geral e os pré-requisitos da funcionalidade de migração.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: 967c03f3c4201bdcf1529fdda93717b6eb74e771
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631693"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrar dados de séries StorSimple 5000 a 7000 para o dispositivo da 8000 série

> [!IMPORTANT]
> - 31 de Julho de 2019 a série StorSimple 5000/7000 chegará ao fim de estado do suporte (término do suporte ao). Recomendamos que os clientes de série StorSimple 5000/7000 migrem para uma das alternativas descritas no documento.
> - A migração é atualmente uma operação assistida. Se pretende migrar dados do seu dispositivo da série StorSimple 5000 a 7000 para um dispositivo da série 8000, terá de agendar migração Support da Microsoft. Support da Microsoft, em seguida, irá ativar a sua subscrição para a migração. Para obter mais informações, consulte como [abra um pedido de suporte](storsimple-8000-contact-microsoft-support.md).
> - Depois que o pedido de serviço de ficheiros, poderá demorar algumas semanas para executar o plano de migração e realmente começar a migração.
> - Antes de contactar o Microsoft Support, certifique-se de que reveja e conclua o [pré-requisitos de migração](#migration-prerequisites) indicado no artigo.

## <a name="overview"></a>Descrição geral

Este artigo apresenta a funcionalidade de migração que permite que os clientes de série de 5000 a 7000 do StorSimple migrar os seus dados para o dispositivo físico da série StorSimple 8000 ou uma aplicação de cloud 8010/8020. Este artigo também contém ligações para instruções passo a passo para download dos passos necessários para migrar dados de um 5000-7000 dispositivo legado da série para uma série 8000 física ou uma aplicação da cloud.

Este artigo se aplica tanto o dispositivo da série 8000 locais, bem como a StorSimple Cloud Appliance.


## <a name="migration-feature-versus-host-side-migration"></a>Funcionalidade de migração em comparação com a migração do lado do host

Pode mover os dados com a funcionalidade de migração ou através de uma migração do lado do host. Esta secção descreve as especificidades de cada método, incluindo os prós e contras. Utilize estas informações para descobrir qual método que deseja buscar para migrar os seus dados.

A funcionalidade de migração simula um processo de recuperação (DR) de desastres da série de 7000/5000 para a 8000 série. Esta funcionalidade permite-lhe migrar os dados do formato de série 5000/7000 para formato de série 8000 no Azure. O processo de migração é iniciado com a ferramenta de migração do StorSimple. A ferramenta é iniciado o download e a conversão de metadados de cópia de segurança no dispositivo de série 8000 e, em seguida, usa a cópia de segurança mais recente para expor os volumes no dispositivo.

|      | Profissionais de TI                                                                                                                                     |Contras                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | O processo de migração preserva o histórico de cópias de segurança que foram executadas no 5000/7000 séries.                                               | Quando os utilizadores tentam aceder os dados, esta migração irá transferir os dados do Azure, portanto, incorrer em custos de transferência de dados.                                     |
| 2.   | Não existem dados são migrados no lado do host.                                                                                                     | O processo precisa de um período de indisponibilidade entre o início da cópia de segurança e mais recente cópia de segurança que está a ser apresentadas na 8000 série (pode ser estimada usando a ferramenta de migração). |
| 3.   | Este processo preserva todas as políticas, modelos de largura de banda, encriptação e outras definições em dispositivos da 8000 série.                      | Acesso de utilizador recuperará apenas os dados acedidos pelos utilizadores e não será realimentar todo o conjunto de dados.                                                  |
| 4.   | Este processo necessita de mais tempo para converter todas as cópias de segurança mais antigas no Azure que é feito de forma assíncrona, sem afetar a produção | Migração só pode ser feita num nível de configuração de cloud.  Volumes individuais numa configuração de cloud não podem ser migradas separadamente                       |

Uma migração do lado do host permite que a configuração da 8000 série de forma independente e copiar os dados do dispositivo da 5000/7000 série para dispositivo da 8000 série. Isso equivale ao migrar dados de um dispositivo de armazenamento para outro. Uma variedade de ferramentas, como Diskboss, robocopy são utilizados para copiar os dados.

|      | Profissionais de TI                                                                                                                      |Contras                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Migração pode ser abordada de forma faseada numa base de volume por volume.                                               | Cópias de segurança anteriores (taken em 5000/7000 séries) não estarão disponíveis no dispositivo da 8000 série.                                                                                                       |
| 2.   | Permite a consolidação de dados para uma conta de armazenamento no Azure.                                                       | Primeira cópia de segurança para a cloud na 8000 série irá demorar mais tempo como todos os dados nas necessidades da 8000 série a cópia de segurança para o Azure.                                                                     |
| 3.   | Após uma migração com êxito, todos os dados é local em que a aplicação. Não há nenhum latências de acesso aos dados. | Consumo de armazenamento do Azure irá aumentar até que os dados são eliminados do 5000/7000 dispositivo.                                                                                                        |
| 4.   |                                                                                                                           | Se o dispositivo da série de 7000/5000 tem uma grande quantidade de dados, durante a migração estes dados tem de ser transferido a partir do azure que irá implicar custos e latências relacionadas com a transferência de dados do Azure |

Este artigo se concentra apenas na funcionalidade de migração de 5000/7000 para o dispositivo da série 8000. Para obter mais informações sobre a migração do lado do host, aceda a [migração de outros dispositivos de armazenamento](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

Seguem-se os pré-requisitos de migração para o seu dispositivo da série 5000 ou 7000 legado e o dispositivo do StorSimple 8000 série.

> [!IMPORTANT]
> Reveja e conclua os pré-requisitos de migração, antes que um pedido de serviço com o Microsoft Support de ficheiros.

### <a name="for-the-50007000-series-device-source"></a>Para o dispositivo de 5000/7000 séries (origem)

Antes de começar a migração, certifique-se de que:

* Tem sua 5000 ou a origem de 7000 séries dispositivos; o dispositivo pode ser live ou reduz verticalmente.

    > [!IMPORTANT]
    > Recomendamos que tem acesso de série a este dispositivo ao longo do processo de migração. Acesso de série deve haver problemas de dispositivo, pode ajudar na resolução de problemas.

* O dispositivo de origem de série de 5000 ou 7000 está em execução v2.1.1.518 de versão do software ou posterior. Versões anteriores não são suportadas.
* Para verificar a versão que está a executar sua série de 5000 ou 7000, dar uma olhada no canto superior direito da sua interface do Usuário da Web. Isto deve apresentar a versão do software que está a executar o seu dispositivo. Para a migração, as séries 5000 ou 7000 devem executar v2.1.1.518.

    ![Verificar a versão de software no dispositivo legado](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Se o dispositivo em direto não está em execução v2.1.1.518 ou posterior, atualize o sistema para a versão mínima necessária. Para obter instruções detalhadas, consulte [actualizar o sistema para v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Se estiver a executar v2.1.1.518, vá para a web da interface do Usuário para ver se existem quaisquer notificações para falhas de restauro do registo. Se tinha falha no restauro do registo, execute o restauro do registo. Para obter mais informações, veja como [executar o restauro do registo](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Se tiver um dispositivo de baixo que não estava a executar o v2.1.1.518, execute uma ativação pós-falha para um dispositivo de substituição que está a executar o v2.1.1.518. Para obter instruções detalhadas, consulte a DR do dispositivo StorSimple da 5000/7000 série.
    * Cópia de segurança os dados para o seu dispositivo, tirando um instantâneo de cloud.
    * Procurar por qualquer outras cópia de segurança tarefas ativas que estão em execução no dispositivo de origem. Isto inclui os trabalhos do host de Console de proteção de dados do StorSimple. Aguarde que as tarefas atuais concluir.


### <a name="for-the-8000-series-physical-device-target"></a>Para o dispositivo físico da 8000 série (destino)

Antes de começar a migração, certifique-se de que:

* O dispositivo da série 8000 do destino seja registrada e em execução. Para obter mais informações, consulte como [implementar o dispositivo StorSimple no serviço StorSimple Manager](storsimple-8000-deployment-walkthrough-u2.md).
* O dispositivo da 8000 série tem a mais recente StorSimple 8000 Series Update 5 instalado e está em execução 6.3.9600.17845 ou versão posterior. Se o seu dispositivo não tem as atualizações mais recentes instaladas, terá de instalar as atualizações mais recentes antes de poder continuar a migração. Para obter mais informações, consulte como [instalar a atualização mais recente no seu dispositivo da 8000 série](storsimple-8000-install-update-5.md).
* A subscrição do Azure está ativada para a migração. Se a sua subscrição não está ativada, contacte o Support da Microsoft para ativar a sua subscrição para a migração.

### <a name="for-the-80108020-cloud-appliance-target"></a>Para a aplicação de cloud 8010/8020 (destino)

Antes de começar a migração, certifique-se:

* A aplicação da cloud de destino seja registrada e em execução. Para obter mais informações, consulte como [implementar e gerir a StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).
* A aplicação da cloud está a executar o mais recente StorSimple 8000 Series Update 5 6.3.9600.17845 de versão do software. Se a aplicação da cloud não estiver a executar a atualização 5, crie uma nova cloud appliance atualização 5 antes de continuar a migração. Para obter mais informações, consulte como [criar uma aplicação da cloud 8010/8020](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Para o computador que executa a ferramenta de migração do StorSimple

Ferramenta de migração do StorSimple é uma ferramenta baseada na interface do Usuário que permite-lhe migrar dados de um StorSimple 5000-7000 série para um dispositivo da série 8000. Para instalar a ferramenta de migração do StorSimple, utilize um computador que cumpre os seguintes requisitos.

O computador tem conectividade à Internet e:

* Com o sistema operativo seguinte
    * Windows 10.
    * Windows Server 2012 R2 (ou superior) para instalar a ferramenta de migração do StorSimple.
* Instalou a .NET 4.5.2.
* Tem um mínimo de 5 GB de espaço livre para instalar e utilizar a ferramenta.

> [!TIP]
> Se o dispositivo StorSimple está ligado a um anfitrião do Windows Server, pode instalar a ferramenta de migração no computador anfitrião Windows Server.

#### <a name="to-install-storsimple-migration-tool"></a>Para instalar a ferramenta de migração do StorSimple

Execute os seguintes passos para instalar a ferramenta de migração do StorSimple no seu computador.

1. Copie a pasta _StorSimple8000SeriesMigrationTool_ para o seu computador Windows. Certifique-se de que a unidade em que o software é copiado tem espaço suficiente.

    Abra o ficheiro de configuração da ferramenta _StorSimple8000SeriesMigrationTool.exe.config_ na pasta. Eis o trecho do arquivo.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Edite os valores correspondentes para as chaves e substitua por:

    * `UserName` – Nome de utilizador a iniciar sessão no portal do Azure.
    * `SubscriptionName and SubscriptionId` – O nome e ID para a sua subscrição do Azure. No seu serviço StorSimple Device Manager página de destino, em **gerais**, clique em **propriedades**. Copie o nome da subscrição e o ID de subscrição associado ao seu serviço.
    * `ResourceName` – O nome do seu serviço StorSimple Device Manager no portal do Azure. Também é apresentado em Propriedades do serviço.
    * `ResourceGroup` – O nome do grupo de recursos associado ao seu serviço StorSimple Device Manager no portal do Azure. Também é apresentado em Propriedades do serviço.
    ![Propriedades do serviço de verificação para o dispositivo de destino](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` – ID do azure Active Directory inquilino no portal do Azure. Inicie sessão no Microsoft Azure como administrador. No portal do Microsoft Azure, clique em **do Azure Active Directory**. Sob **Manage**, clique em **propriedades**. O inquilino ID é mostrado na **ID de diretório** caixa.
    ![Verifique o ID de inquilino para o Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Guarde as alterações efetuadas ao ficheiro de configuração.
4.  Executar o _StorSimple8000SeriesMigrationTool.exe_ para iniciar a ferramenta. Quando lhe forem pedidas credenciais, forneça as credenciais associadas com a sua subscrição no portal do Azure. 
5.  A ferramenta de migração do StorSimple da interface do Usuário é apresentada.
  

## <a name="next-steps"></a>Passos Seguintes
Transferir as instruções passo a passo sobre como [migrar dados do StorSimple 5000-7000 série para um dispositivo da 8000 série](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).
