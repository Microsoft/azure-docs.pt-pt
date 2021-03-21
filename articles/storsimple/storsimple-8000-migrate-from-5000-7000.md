---
title: Migrar dados sobre o dispositivo da série StorSimple 5000-7000 para 8000| Microsoft Docs
description: Saiba mais sobre os dados migratórios do dispositivo da série StorSimple 5000-7000 para 8000 e quais são os pré-requisitos para o processo de migração.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 58b7b67842e9ba385b34ea4d8fdbcac190076218
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99428171"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrar dados da série StorSimple 5000-7000 para 8000

> [!IMPORTANT]
> - No dia 31 de julho de 2019, a série StorSimple 5000/7000 chegará ao fim do estado de suporte (EOS). Recomendamos que os clientes da série StorSimple 5000/7000 migram para uma das alternativas descritas no documento.
> - A migração é atualmente uma operação assistida. Se pretender migrar dados do seu dispositivo da série StorSimple 5000-7000 para um dispositivo de série 8000, tem de agendar a migração com o Microsoft Support. O Microsoft Support ativará então a sua subscrição para migração. Para mais informações, consulte como [abrir um bilhete de suporte.](storsimple-8000-contact-microsoft-support.md)
> - Depois de apresentar o pedido de serviço, pode levar algumas semanas para executar o plano de migração e realmente iniciar a migração.
> - Antes de contactar o Microsoft Support, certifique-se de rever e completar os [pré-requisitos de Migração indicados](#migration-prerequisites) no artigo.

## <a name="overview"></a>Descrição geral

Este artigo introduz a funcionalidade de migração que permite aos clientes da série StorSimple 5000-7000 migrarem os seus dados para o dispositivo físico da série StorSimple 8000 ou para um aparelho em nuvem 8010/8020. Este artigo também se liga a uma passagem passo a passo das etapas necessárias para migrar dados de um dispositivo legado da série 5000-7000 para um aparelho físico ou em nuvem de série 8000.

Este artigo aplica-se tanto ao dispositivo da série 8000 no local como ao StorSimple Cloud Appliance.


## <a name="migration-feature-versus-host-side-migration"></a>Recurso de migração contra migração do lado do anfitrião

Pode mover os seus dados utilizando a funcionalidade de migração ou realizando uma migração do lado do anfitrião. Esta secção descreve as especificidades de cada método, incluindo os prós e os contras. Utilize estas informações para descobrir que método pretende seguir para migrar os seus dados.

A funcionalidade de migração simula um processo de recuperação de desastres (DR) de séries 7000/5000 para 8000. Esta funcionalidade permite migrar os dados do formato da série 5000/7000 para o formato da série 8000 no Azure. O processo de migração é iniciado utilizando a ferramenta migração StorSimple. A ferramenta inicia o download e a conversão de metadados de backup no dispositivo da série 8000 e, em seguida, utiliza a cópia de segurança mais recente para expor os volumes no dispositivo.

| Vantagens                                                                                                                                     |Desvantagens                                                                                                                                                              |
|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| O processo de migração preserva a história dos backups que foram feitos em séries 5000/7000.                                               | Quando os utilizadores tentam aceder aos dados, esta migração irá descarregar os dados do Azure, incorrendo assim nos custos de descarregamento de dados.                                     |
| Nenhum dado é migrado no lado hospedeiro.                                                                                                     | O processo necessita de tempo de inatividade entre o início da cópia de segurança e a última cópia de segurança que está a ser publicada na série 8000 (pode estimar-se com a ferramenta de migração). |
| Este processo preserva todas as políticas, modelos de largura de banda, encriptação e outras configurações em dispositivos da série 8000.                      | O acesso ao utilizador trará apenas os dados acedidos pelos utilizadores e não irá hidratar todo o conjunto de dados.                                                  |
| Este processo requer tempo adicional para converter todos os backups mais antigos em Azure, que é feito assíncronamente sem afetar a produção | A migração só pode ser feita a um nível de configuração em nuvem.  Volumes individuais numa configuração de nuvem não podem ser migrados separadamente                       |

Uma migração do lado do anfitrião permite configurar a série 8000 de forma independente e copiar os dados de dispositivo da série 5000/7000 para dispositivo da série 8000. Isto equivale a migrar dados de um dispositivo de armazenamento para outro. Uma variedade de ferramentas como Diskboss, robocopia são usadas para copiar os dados.

| Vantagens                                                                                                                      |Desvantagens                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A migração pode ser abordada de forma faseada numa base volume a volume.                                               | Cópias de segurança anteriores (tomadas em série 5000/7000) não estarão disponíveis no dispositivo da série 8000.                                                                                                       |
| Permite a consolidação de dados numa única conta de armazenamento no Azure.                                                       | O primeiro backup para a nuvem na série 8000 levará mais tempo, uma vez que todos os dados da série 8000 precisam de ser apoiados até Azure.                                                                     |
| Após uma migração bem sucedida, todos os dados são locais no aparelho. Não há latências no acesso aos dados. | O consumo de armazenamento Azure aumentará até que os dados sejam eliminados do dispositivo 5000/7000.                                                                                                        |
|                                                                                                                           | Se o dispositivo da série 7000/5000 tiver uma grande quantidade de dados, durante a migração estes dados precisam de ser descarregados do Azure, o que incorrerá em custos e latências relacionadas com o descarregamento de dados do Azure |

Este artigo centra-se apenas na funcionalidade de migração de 5000/7000 a 8000 dispositivos da série. Para obter mais informações sobre a migração do lado do hospedeiro, vá para a [Migração de outros dispositivos de armazenamento](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Pré-requisitos de migração

Aqui estão os pré-requisitos de migração para o seu dispositivo da série 5000 ou 7000 e o dispositivo StorSimple da série 8000.

> [!IMPORTANT]
> Reveja e preencha os pré-requisitos de migração antes de apresentar um pedido de serviço com o Microsoft Support.

### <a name="for-the-50007000-series-device-source"></a>Para o dispositivo da série 5000/7000 (fonte)

Antes de começar a migração, certifique-se de que:

* Tem o seu dispositivo de origem da série 5000 ou 7000; o dispositivo pode ser vivo ou para baixo.

    > [!IMPORTANT]
    > Recomendamos que tenha acesso em série a este dispositivo durante todo o processo de migração. Se houver algum problema com o dispositivo, o acesso em série pode ajudar na resolução de problemas.

* O seu dispositivo de origem da série 5000 ou 7000 está a executar a versão de software v2.1.1.518 ou posterior. Versões anteriores não são suportadas.
* Para verificar a versão que a sua série 5000 ou 7000 está em execução, olhe para o canto superior direito da sua UI Web. Isto deve apresentar a versão de software que o seu dispositivo está a executar. Para a migração, a sua série de 5000 ou 7000 deve estar a correr v2.1.1.518.

    ![Verifique a versão do software no dispositivo legado](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Se o seu dispositivo vivo não estiver a funcionar v2.1.1.518 ou posterior, por favor atualize o seu sistema para a versão mínima necessária. Poderá ter de trabalhar com o Microsoft Support para o ajudar a realizar a atualização.
    * Se estiver a executar v2.1.1.518, vá à Web UI para ver se existem notificações para falhas de restauro do registo. Se a restauração do registo tivesse falhado, executar o registo restabelecendo. Poderá ter de trabalhar com o Microsoft Support para o ajudar a restaurar o seu registo.
    * Se tiver um dispositivo para baixo que não esteja a funcionar v2.1.1.518, efetue uma falha num dispositivo de substituição que esteja a funcionar v2.1.1.518. Para obter instruções detalhadas, consulte o DR do seu dispositivo StorSimple da série 5000/7000.
    * Revendo os dados para o seu dispositivo tirando uma fotografia em nuvem.
    * Verifique se existem outros trabalhos de backup ativos que estejam a ser geridos no dispositivo de origem. Isto inclui os trabalhos no anfitrião da consola de proteção de dados StorSimple. Espere que os trabalhos atuais terminem.


### <a name="for-the-8000-series-physical-device-target"></a>Para o dispositivo físico da série 8000 (alvo)

Antes de começar a migração, certifique-se de que:

* O seu dispositivo da série target 8000 está registado e em funcionamento. Para obter mais informações, consulte como [implementar o seu dispositivo StorSimple com o serviço StorSimple Manager](storsimple-8000-deployment-walkthrough-u2.md).
* O seu dispositivo da série 8000 tem o mais recente StorSimple Série Update 5 instalado e está a correr 6.3.9600.17845 ou versão posterior. Se o seu dispositivo não tiver as últimas atualizações instaladas, tem de instalar as últimas atualizações antes de poder proceder à migração. Para mais informações, consulte como instalar a [mais recente atualização no seu dispositivo da série 8000](storsimple-8000-install-update-5.md).
* A sua subscrição Azure está ativada para migração. Se a sua subscrição não estiver ativada, contacte o Microsoft Support para ativar a sua subscrição para migração.

### <a name="for-the-80108020-cloud-appliance-target"></a>Para o aparelho em nuvem 8010/8020 (alvo)

Antes de iniciar a migração, certifique-se:

* O seu aparelho em nuvem-alvo está registado e em funcionamento. Para obter mais informações, consulte como [implementar e gerir o StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).
* O seu aparelho em nuvem está a executar a versão de software 5 da série StorSimple 8000 5. Se o seu aparelho em nuvem não estiver a funcionar A atualização 5, crie um novo aparelho em nuvem Update 5 antes de proceder à migração. Para mais informações, consulte como [criar um aparelho em nuvem 8010/8020.](storsimple-8000-cloud-appliance-u2.md)

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Para o computador que executa a ferramenta de migração StorSimple

A ferramenta StorSimple Migration é uma ferramenta baseada em UI que permite migrar dados de uma série StorSimple 5000-7000 para um dispositivo de série 8000. Para instalar a ferramenta migração StorSimple, utilize um computador que satisfaça os seguintes requisitos.

O computador tem conectividade com a Internet e:

* Está a executar o seguinte sistema operativo
    * Windows 10.
    * Windows Server 2012 R2 (ou superior) para instalar a ferramenta de migração StorSimple.
* Tem .NET 4.5.2 instalado.
* Tem um mínimo de 5 GB de espaço livre para instalar e utilizar a ferramenta.

> [!TIP]
> Se o seu dispositivo StorSimple estiver ligado a um anfitrião do Windows Server, pode instalar a ferramenta de migração no computador anfitrião do Windows Server.

#### <a name="to-install-storsimple-migration-tool"></a>Para instalar a ferramenta de migração StorSimple

Execute os seguintes passos para instalar a ferramenta de migração StorSimple no seu computador.

1. Copie a pasta _StorSimple8000SeriesMigrationTool_ para o seu computador Windows. Certifique-se de que a unidade onde o software é copiado tem espaço suficiente.

    Abra o ficheiro configurar a ferramenta _StorSimple8000SeriesMigrationTool.exe.config_ na pasta. Aqui está o corte do ficheiro.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Editar os valores correspondentes às teclas e substituir por:

    * `UserName` – Nome do utilizador para iniciar sessão no portal Azure.
    * `SubscriptionName and SubscriptionId` – Nome e ID para a sua assinatura Azure. Na sua página de aterragem de serviço StorSimple Device Manager, em **Geral,** clique em **Propriedades**. Copie o nome de assinatura e o ID de assinatura associado ao seu serviço.
    * `ResourceName` – Nome do seu serviço StorSimple Device Manager no portal Azure. Também mostrado em propriedades de serviço.
    * `ResourceGroup` – Nome do grupo de recursos associado ao seu serviço StorSimple Device Manager no portal Azure. Também mostrado em propriedades de serviço.
    ![Verifique as propriedades do serviço para o dispositivo alvo](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` – ID do Azure Ative Directory Tenanty no portal Azure. Faça login no Microsoft Azure como administrador. No portal Microsoft Azure, clique em **Azure Ative Directory**. Em **Gerir**, clique em **Propriedades**. A identificação do inquilino é mostrada na caixa de identificação do **Diretório.**
    ![Verifique a ID do Inquilino para O Diretório Ativo Azure](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Guarde as alterações efetuadas no ficheiro config.
4.  Executar o _StorSimple8000SeriesMigrationTool.exe_ para lançar a ferramenta. Quando solicitado para obter credenciais, forneça as credenciais associadas à sua subscrição no portal Azure. 
5.  É apresentada a ferramenta de migração StorSimple UI.
  

## <a name="next-steps"></a>Passos seguintes
Descarregue as instruções passo a passo sobre como [migrar dados de uma série StorSimple 5000-7000 para um dispositivo de série 8000](https://databoxbuilds.blob.core.windows.net/storsimpledocs/MicrosoftAzureStorSimple_Migration_Guide_20171115.pdf).
