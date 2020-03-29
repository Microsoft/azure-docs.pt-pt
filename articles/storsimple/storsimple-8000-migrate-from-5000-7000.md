---
title: Migrar dados da série StorSimple 5000-7000 para o dispositivo da série 8000. Microsoft Docs
description: Fornece uma visão geral e os pré-requisitos da funcionalidade Migração.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60631693"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrar dados da série StorSimple 5000-7000 para o dispositivo da série 8000

> [!IMPORTANT]
> - Em 31 de julho de 2019, a série StorSimple 5000/7000 chegará ao fim do estatuto de suporte (EOS). Recomendamos que os clientes da série StorSimple 5000/7000 migram para uma das alternativas descritas no documento.
> - A migração é atualmente uma operação assistida. Se pretender migrar dados do seu dispositivo da série StorSimple 5000-7000 para um dispositivo da série 8000, precisa de agendar a migração com o Microsoft Support. O Microsoft Support irá então ativar a sua subscrição para migração. Para mais informações, consulte como abrir um bilhete de [suporte.](storsimple-8000-contact-microsoft-support.md)
> - Depois de apresentar o pedido de serviço, pode levar algumas semanas para executar o plano de migração e realmente iniciar a migração.
> - Antes de contactar o Microsoft Support, certifique-se de que revê e completa os [pré-requisitos de Migração indicados](#migration-prerequisites) no artigo.

## <a name="overview"></a>Descrição geral

Este artigo introduz a funcionalidade de migração que permite aos clientes da série StorSimple 5000-7000 migrarem os seus dados para o dispositivo físico da série StorSimple 8000 ou para um aparelho em nuvem 8010/8020. Este artigo também liga a uma passagem passo a passo dos passos necessários para migrar dados de um dispositivo legado da série 5000-7000 para um aparelho físico ou cloud série 8000.

Este artigo é aplicável tanto para o dispositivo da série 8000 no local como para o StorSimple Cloud Appliance.


## <a name="migration-feature-versus-host-side-migration"></a>Característica de migração versus migração do lado do hospedeiro

Pode mover os seus dados utilizando a funcionalidade de migração ou realizando uma migração do lado do hospedeiro. Esta secção descreve as especificidades de cada método, incluindo os prós e os contras. Utilize esta informação para descobrir que método pretende seguir para migrar os seus dados.

A funcionalidade de migração simula um processo de recuperação de desastres (DR) da série 7000/5000 para 8000 séries. Esta funcionalidade permite-lhe migrar os dados do formato da série 5000/7000 para o formato da série 8000 no Azure. O processo de migração é iniciado utilizando a ferramenta StorSimple Migration. A ferramenta inicia o download e a conversão de metadados de backup no dispositivo da série 8000 e, em seguida, utiliza a mais recente cópia de segurança para expor os volumes no dispositivo.

|      | Vantagens                                                                                                                                     |Contras                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | O processo de migração preserva a história dos backups que foram levados em séries 5000/7000.                                               | Quando os utilizadores tentam aceder aos dados, esta migração irá descarregar os dados do Azure, incorrendo assim nos custos de descarregamento de dados.                                     |
| 2.   | Não são migrados dados do lado do hospedeiro.                                                                                                     | O processo necessita de tempo de inatividade entre o início da cópia de segurança e a última cópia de segurança que está a ser divulgada na série 8000 (pode estimar-se utilizando a ferramenta de migração). |
| 3.   | Este processo preserva todas as políticas, modelos de largura de banda, encriptação e outras definições em dispositivos da série 8000.                      | O acesso ao utilizador trará de volta apenas os dados acedidos pelos utilizadores e não irá reidratar todo o conjunto de dados.                                                  |
| 4.   | Este processo requer mais tempo para converter todos os backups mais antigos em Azure, o que é feito assincronicamente sem afetar a produção | A migração só pode ser feita a um nível de configuração de nuvem.  Os volumes individuais numa configuração de nuvem não podem ser migrados separadamente                       |

Uma migração do lado do hospedeiro permite a configuração de 8000 séries de forma independente e copiar os dados de dispositivo da série 5000/7000 para dispositivo da série 8000. Isto equivale a dados migratórios de um dispositivo de armazenamento para outro. Uma variedade de ferramentas como Diskboss, robocopia são usadas para copiar os dados.

|      | Vantagens                                                                                                                      |Contras                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | A migração pode ser abordada de forma faseada numa base volume-a-volume.                                               | As cópias de segurança anteriores (tomadas na série 5000/7000) não estarão disponíveis no dispositivo da série 8000.                                                                                                       |
| 2.   | Permite a consolidação de dados numa única conta de armazenamento no Azure.                                                       | O primeiro backup para a nuvem na série 8000 levará mais tempo, uma vez que todos os dados da série 8000 precisam de ser apoiados até ao Azure.                                                                     |
| 3.   | Após uma migração bem sucedida, todos os dados são locais no aparelho. Não há atrasos no acesso aos dados. | O consumo de armazenamento azure aumentará até que os dados sejam eliminados do dispositivo 5000/7000.                                                                                                        |
| 4.   |                                                                                                                           | Se o dispositivo da série 7000/5000 tiver uma grande quantidade de dados, durante a migração estes dados precisam de ser descarregados do Azure, que incorrerá em custos e tardios relacionados com o descarregamento de dados do Azure |

Este artigo centra-se apenas na funcionalidade de migração de 5000/7000 para 8000 dispositivos da série. Para obter mais informações sobre a migração do lado do hospedeiro, vá para a [Migração a partir de outros dispositivos de armazenamento](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

Aqui estão os pré-requisitos de migração para o seu legacy 5000 ou 7000 dispositivo da série e o dispositivo 8000 série StorSimple.

> [!IMPORTANT]
> Reveja e complete os pré-requisitos de migração antes de apresentar um pedido de serviço com o Microsoft Support.

### <a name="for-the-50007000-series-device-source"></a>Para o dispositivo da série 5000/7000 (fonte)

Antes de começar a migração, certifique-se de que:

* Tem o seu dispositivo de origem da série 5000 ou 7000; o dispositivo pode ser vivo ou para baixo.

    > [!IMPORTANT]
    > Recomendamos que tenha acesso em série a este dispositivo durante todo o processo de migração. Se houver algum problema no dispositivo, o acesso em série pode ajudar na resolução de problemas.

* O seu dispositivo de origem da série 5000 ou 7000 está a executar a versão de software v2.1.1.518 ou mais tarde. Versões anteriores não são suportadas.
* Para verificar a versão que a sua série 5000 ou 7000 está em execução, veja o canto superior direito da sua Web UI. Isto deve mostrar a versão de software que o seu dispositivo está a executar. Para a migração, a sua série 5000 ou 7000 deve estar em execução v2.1.1.518.

    ![Verifique a versão do software no dispositivo legado](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Se o seu dispositivo ao vivo não estiver a funcionar v2.1.1.518 ou mais tarde, por favor atualize o seu sistema para a versão mínima necessária. Para obter instruções detalhadas, consulte o [upgrade do seu sistema para v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Se estiver a executar v2.1.1.518, vá à Web UI para ver se existem notificações para falhas de restauro de registo. Se o registo restaurado falhou, executar o registo restaurar. Para mais informações, vá a como [executar a restauração do registo](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Se tiver um dispositivo de avaria que não estivesse a funcionar v2.1.1.518, execute uma falha num dispositivo de substituição que esteja a funcionar v2.1.1.518. Para obter instruções detalhadas, consulte o DR do seu dispositivo StorSimple série 5000/7000.
    * Recue os dados do seu dispositivo tirando uma imagem em nuvem.
    * Verifique se existem outros trabalhos de backup ativos que estejam a funcionar no dispositivo de origem. Isto inclui os trabalhos no anfitrião da Consola de Proteção de Dados StorSimple. Espere que os trabalhos atuais completem.


### <a name="for-the-8000-series-physical-device-target"></a>Para o dispositivo físico da série 8000 (alvo)

Antes de começar a migração, certifique-se de que:

* O seu dispositivo da série 8000 está registado e em execução. Para mais informações, consulte como [implementar o seu dispositivo StorSimple com o serviço StorSimple Manager](storsimple-8000-deployment-walkthrough-u2.md).
* O seu dispositivo da série 8000 tem o mais recente StorSimple 8000 Series Update 5 instalado e está a executar 6.3.9600.17845 ou versão posterior. Se o seu dispositivo não tiver as últimas atualizações instaladas, tem de instalar as últimas atualizações antes de poder avançar com a migração. Para mais informações, consulte como instalar a última atualização no seu dispositivo da [série 8000](storsimple-8000-install-update-5.md).
* A sua subscrição Azure está ativada para a migração. Se a sua subscrição não estiver ativada, contacte o Microsoft Support para ativar a sua subscrição para migração.

### <a name="for-the-80108020-cloud-appliance-target"></a>Para o aparelho de nuvem 8010/8020 (alvo)

Antes de começar a migração, certifique-se:

* O seu aparelho de nuvem alvo está registado e em funcionamento. Para mais informações, consulte como implementar e gerir o [StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).
* O seu aparelho em nuvem está a funcionar com a mais recente versão de software StorSimple 8000 Series Update 5 6.3.3.9600.17845. Se o aparelho em nuvem não estiver a funcionar A atualizar 5, crie um novo aparelho em nuvem Update 5 antes de avançar com a migração. Para mais informações, consulte como criar um aparelho de [nuvem 8010/8020](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Para o computador que executa a ferramenta StorSimple Migration

A ferramenta StorSimple Migration é uma ferramenta baseada em UI que permite migrar dados de uma série StorSimple 5000-7000 para um dispositivo da série 8000. Para instalar a ferramenta StorSimple Migration, utilize um computador que satisfaça os seguintes requisitos.

O computador tem conectividade na Internet e:

* Está a executar o seguinte sistema operativo
    * Janelas 10.
    * Windows Server 2012 R2 (ou superior) para instalar a ferramenta StorSimple Migration.
* Tem .NET 4.5.2 instalado.
* Tem um mínimo de 5 GB de espaço livre para instalar e utilizar a ferramenta.

> [!TIP]
> Se o seu dispositivo StorSimple estiver ligado a um anfitrião do Windows Server, pode instalar a ferramenta de migração no computador anfitrião do Windows Server.

#### <a name="to-install-storsimple-migration-tool"></a>Para instalar a ferramenta StorSimple Migration

Execute os seguintes passos para instalar a ferramenta StorSimple Migration no seu computador.

1. Copie a pasta _StorSimple8000SeriesMigrationTool_ para o seu computador Windows. Certifique-se de que a unidade onde o software é copiado tem espaço suficiente.

    Abra o ficheiro de config da ferramenta _StorSimple8000SeriesMigrationTool.exe.config_ na pasta. Aqui está o corte do ficheiro.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Editar os valores correspondentes às teclas e substituir por:

    * `UserName`– Nome de utilizador para iniciar sessão no portal Azure.
    * `SubscriptionName and SubscriptionId`– Nome e ID para a sua subscrição Azure. Na sua página de aterragem de serviço StorSimple Device Manager, em **Geral,** clique em **Propriedades**. Copie o nome de subscrição e o ID de subscrição associado ao seu serviço.
    * `ResourceName`– Nome do seu serviço StorSimple Device Manager no portal Azure. Também mostrado sob propriedades de serviço.
    * `ResourceGroup`– Nome do grupo de recursos associado ao seu serviço StorSimple Device Manager no portal Azure. Também mostrado sob propriedades de serviço.
    ![Verifique as propriedades do serviço para o dispositivo-alvo](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId`– Azure Ative Directory Tenant ID no portal Azure. Inicie sessão no Microsoft Azure como administrador. No portal Microsoft Azure, clique no **Azure Ative Directory**. Em **Gerir**, clique em **Propriedades**. A identificação do inquilino está mostrada na caixa de identificação do **Diretório.**
    ![Consulte o ID do inquilino para o Diretório Ativo Azure](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Guarde as alterações feitas no ficheiro config.
4.  Executar o _StorSimple8000SeriesMigrationTool.exe_ para lançar a ferramenta. Quando solicitado para credenciais, forneça as credenciais associadas à sua subscrição no portal Azure. 
5.  A ferramenta StorSimple Migration UI é apresentada.
  

## <a name="next-steps"></a>Passos seguintes
Descarregue as instruções passo a passo sobre como [migrar dados de uma série StorSimple 5000-7000 para um dispositivo da série 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).
