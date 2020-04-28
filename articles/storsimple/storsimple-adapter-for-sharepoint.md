---
title: Instale o Adaptador StorSimple para o SharePoint [ Microsoft Docs
description: Descreve como instalar e configurar ou remover o Adaptador StorSimple para O SharePoint numa exploração de servidores SharePoint.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: twooley
ms.openlocfilehash: a841ce8b664389ccd8fdf55de9965f09412fecf5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75930212"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instale e configure o Adaptador StorSimple para O SharePoint
## <a name="overview"></a>Descrição geral
O Adaptador StorSimple para O SharePoint é um componente que permite fornecer ao Microsoft Azure StorSimple armazenamento flexível e proteção de dados para as explorações de servidores SharePoint. Pode utilizar o adaptador para mover conteúdo de objeto grande binário (BLOB) das bases de dados de conteúdo do SQL Server para o dispositivo de armazenamento em nuvem híbrido Microsoft Azure StorSimple.

O Adaptador StorSimple para o SharePoint funciona como um fornecedor de armazenamento de BLOB remoto (RBS) e utiliza a funcionalidade de armazenamento remoto blob do Servidor SQL para armazenar conteúdo deSharePoint não estruturado (sob a forma de BLOBs) num servidor de ficheiros que é apoiado por um dispositivo StorSimple.

> [!NOTE]
> O Adaptador StorSimple para SharePoint suporta o Armazenamento BLOB Remoto SharePoint Server 2010 (RBS). Não suporta o SharePoint Server 2010 External BLOB Storage (EBS).


* Para descarregar o Adaptador StorSimple para O SharePoint, vá ao [Adaptador StorSimple para O SharePoint][1] no Microsoft Download Center.
* Para obter informações sobre o planeamento das limitações do RBS e do RBS, vá à [Decisão de utilizar o RBS no SharePoint 2013][2] ou no Plano para O RBS [(SharePoint Server 2010)][3].

O resto desta visão geral descreve brevemente o papel do Adaptador StorSimple para o SharePoint e os limites de capacidade e desempenho do SharePoint que deve ter em conta antes de instalar e configurar o adaptador. Depois de rever estas informações, vá ao [Adaptador StorSimple para a instalação do SharePoint](#storsimple-adapter-for-sharepoint-installation) para começar a configurar o adaptador.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>Adaptador StorSimple para benefícios do SharePoint
Num site do SharePoint, o conteúdo é armazenado como dados BLOB não estruturados numa ou mais bases de dados de conteúdo. Por padrão, estas bases de dados estão alojadas em computadores que estão a executar o SQL Server e estão localizados na quinta do servidor SharePoint. Os BLOBs podem aumentar rapidamente de tamanho, consumindo grandes quantidades de armazenamento no local. Por esta razão, talvez queira encontrar outra solução de armazenamento menos dispendiosa. O SQL Server fornece uma tecnologia chamada Remote Blob Storage (RBS) que permite armazenar conteúdo BLOB no sistema de ficheiros, fora da base de dados do Servidor SQL. Com o RBS, os BLOBs podem residir no sistema de ficheiros no computador que está a executar o SQL Server, ou podem ser armazenados no sistema de ficheiros noutro computador do servidor.

O RBS exige que utilize um fornecedor de RBS, como o Adaptador StorSimple para O SharePoint, para ativar o RBS no SharePoint. O Adaptador StorSimple para O SharePoint funciona com o RBS, permitindo-lhe mover BLOBs para um servidor apoiado pelo sistema Microsoft Azure StorSimple. O Microsoft Azure StorSimple armazena os dados BLOB localmente ou na nuvem, com base na utilização. BloBs que são muito ativos (tipicamente referidos como dados de nível 1 ou hot) residem localmente. Dados menos ativos e dados de arquivo residem na nuvem. Depois de ativar o RBS numa base de dados de conteúdos, qualquer novo conteúdo BLOB criado no SharePoint é armazenado no dispositivo StorSimple e não na base de dados de conteúdos.

A implementação do Microsoft Azure StorSimple do RBS proporciona os seguintes benefícios:

* Ao mover o conteúdo BLOB para um servidor separado, pode reduzir a carga de consulta no Servidor SQL, o que pode melhorar a capacidade de resposta do Servidor SQL. 
* O Azure StorSimple utiliza a desduplicação e a compressão para reduzir o tamanho dos dados.
* O Azure StorSimple fornece proteção de dados sob a forma de instantâneos locais e em nuvem. Além disso, se colocar a base de dados no dispositivo StorSimple, pode fazer o reexame da base de dados de conteúdos e bloBs de forma consistente. (Mover a base de dados de conteúdo para o dispositivo só é suportado para o dispositivo da série StorSimple 8000. Esta funcionalidade não é suportada para a série 5000 ou 7000.)
* O Azure StorSimple inclui funcionalidades de recuperação de desastres, incluindo a recuperação de falhas, ficheiros e volumes (incluindo a recuperação de testes) e a rápida recuperação de dados.
* Pode utilizar software de recuperação de dados, como Kroll Ontrack PowerControls, com instantâneos StorSimple de dados BLOB para realizar a recuperação do conteúdo do SharePoint ao nível do item. (Este software de recuperação de dados é uma compra separada.)
* O Adaptador StorSimple para o SharePoint liga-se ao portal da Administração Central sharePoint, permitindo-lhe gerir toda a sua solução SharePoint a partir de uma localização central.

Mover o conteúdo blob para o sistema de ficheiros pode fornecer outras economias e benefícios de custos. Por exemplo, a utilização do RBS pode reduzir a necessidade de armazenamento caro de Nível 1 e, como reduz a base de dados de conteúdos, o RBS pode reduzir o número de bases de dados necessárias na exploração de servidores sharePoint. No entanto, outros fatores, como os limites de tamanho da base de dados e a quantidade de conteúdo não RBS, também podem afetar os requisitos de armazenamento. Para obter mais informações sobre os custos e benefícios da utilização do RBS, consulte [Plan for RBS (SharePoint Foundation 2010)][4] e decidindo utilizar o [RBS no SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Limites de capacidade e desempenho
Antes de considerar utilizar o RBS na sua solução SharePoint, deve estar ciente dos limites de desempenho e capacidade testados do SharePoint Server 2010 e do SharePoint Server 2013, e de como estes limites se relacionam com o desempenho aceitável. Para mais informações, consulte [limites e limites de software para o SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Reveja o seguinte antes de configurar o RBS:

* Certifique-se de que o tamanho total do conteúdo (o tamanho de uma base de dados de conteúdo mais o tamanho de quaisquer BLOBs externos associados) não excede o limite de tamanho DO RBS suportado pelo SharePoint. Este limite é de 200 GB. 
  
    **Para medir a base de dados de conteúdos e o tamanho blob**
  
  1. Execute esta consulta na Administração Central WFE. Inicie a Shell de Gestão sharePoint e, em seguida, introduza o seguinte comando Windows PowerShell para obter o tamanho das bases de dados de conteúdo:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Este passo obtém o tamanho da base de dados de conteúdo no disco.
  2. Execute uma das seguintes consultas SQL no SQL Management Studio na caixa de servidor SQL em cada base de dados de conteúdo, e adicione o resultado ao número obtido no passo 1.
     
     Nas bases de dados de conteúdos SharePoint 2013, introduza:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     Nas bases de dados de conteúdos SharePoint 2010, introduza:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Este passo obtém o tamanho dos BLOBs que foram externalizados.
* Recomendamos que guarde todos os conteúdos blob e base de dados localmente no dispositivo StorSimple. O dispositivo StorSimple é um cluster de dois nós para alta disponibilidade. A colocação das bases de dados de conteúdo e bloBs no dispositivo StorSimple proporciona uma elevada disponibilidade.
  
    Utilize as práticas tradicionais de migração do SQL Server para mover a base de dados de conteúdos para o dispositivo StorSimple. Apenas mova a base de dados depois de todo o conteúdo blob da base de dados ter sido transferido para a partilha de ficheiros através do RBS. Se optar por mover a base de dados de conteúdo para o dispositivo StorSimple, recomendamos que configure o armazenamento da base de dados de conteúdos no dispositivo como um volume primário.
* No Microsoft Azure StorSimple, se utilizar volumes hierárquicos, não há forma de garantir que os conteúdos armazenados localmente no dispositivo StorSimple não serão nividamente para armazenamento em nuvem do Microsoft Azure. Por isso, recomendamos a utilização de volumes storSimple fixados localmente em conjunto com o SharePoint RBS. Isto garantirá que todos os conteúdos BLOB permanecem localmente no dispositivo StorSimple, e não são transferidos para o Microsoft Azure.
* Se não armazenar as bases de dados de conteúdo no dispositivo StorSimple, utilize as tradicionais práticas de alta disponibilidade do SQL Server que suportam o RBS. O clustering do Servidor SQL suporta o RBS, enquanto o espelhamento do Servidor SQL não. 

> [!WARNING]
> Se não tiver ativado o RBS, não recomendamos que a transferência da base de dados de conteúdos para o dispositivo StorSimple. Esta é uma configuração não testada.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>Adaptador StorSimple para instalação sharePoint
Antes de poder instalar o Adaptador StorSimple para o SharePoint, tem de configurar o dispositivo StorSimple e certificar-se de que a exploração do servidor SharePoint e a instantânea do Servidor SQL cumprem todos os pré-requisitos. Este tutorial descreve os requisitos de configuração, bem como procedimentos para instalar e atualizar o Adaptador StorSimple para O SharePoint.

## <a name="configure-prerequisites"></a>Configurar pré-requisitos
Antes de poder instalar o Adaptador StorSimple para o SharePoint, certifique-se de que o dispositivo StorSimple, a exploração do servidor SharePoint e a instantânea do Servidor SQL cumprem os seguintes pré-requisitos.

### <a name="system-requirements"></a>Requisitos de sistema
O Adaptador StorSimple para O SharePoint funciona com o seguinte hardware e software:

* Sistema operativo suportado – Windows Server 2008 R2 SP1, Windows Server 2012 ou Windows Server 2012 R2
* Versões SharePoint suportadas – SharePoint Server 2010 ou SharePoint Server 2013
* Versões Suportadas SQL Server – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition, ou SQL Server 2012 Enterprise Edition
* Dispositivos StorSimple suportados – Série StorSimple 8000, série StorSimple 7000 ou série StorSimple 5000.

### <a name="storsimple-device-configuration-prerequisites"></a>Pré-requisitos de configuração do dispositivo StorSimple
O dispositivo StorSimple é um dispositivo de bloco e, como tal, requer um servidor de ficheirono qual os dados podem ser hospedados. Recomendamos que utilize um servidor separado em vez de um servidor existente da quinta SharePoint. Este servidor de ficheiros deve estar na mesma rede local de área (LAN) que o computador SQL Server que acolhe as bases de dados de conteúdo.

> [!TIP]
> * Se configurar a sua quinta SharePoint para uma elevada disponibilidade, deverá implementar o servidor de ficheiros para uma elevada disponibilidade também.
> * Se não armazenar a base de dados de conteúdos no dispositivo StorSimple, utilize as tradicionais boas práticas de alta disponibilidade que suportam o RBS. O clustering do Servidor SQL suporta o RBS, enquanto o espelhamento do Servidor SQL não. 


Certifique-se de que o seu dispositivo StorSimple está configurado corretamente e que os volumes adequados para suportar a implementação do SharePoint estão configurados e acessíveis a partir do seu computador SQL Server. Vá implementar [o seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md) se ainda não tiver implementado e configurado o seu dispositivo StorSimple. Note o endereço IP do dispositivo StorSimple; necessitará dele durante o Adaptador StorSimple para a instalação do SharePoint.

Além disso, certifique-se de que o volume a utilizar para a externalização blob satisfaz os seguintes requisitos:

* O volume deve ser formatado com uma unidade de alocação de 64 KB.
* A sua extremidade frontal web (WFE) e servidores de aplicações devem poder aceder ao volume através de um caminho da Convenção Universal de Nomeação (UNC).
* A quinta do servidor SharePoint deve ser configurada para escrever ao volume.

> [!NOTE]
> Depois de instalar e configurar o adaptador, toda a externalização BLOB deve passar pelo dispositivo StorSimple (o dispositivo apresentará os volumes ao SQL Server e gerirá os níveis de armazenamento). Não é possível utilizar outros alvos para a externalização blob.


Se planeia utilizar o StorSimple Snapshot Manager para tirar fotografias dos dados blob e da base de dados, certifique-se de instalar o StorSimple Snapshot Manager no servidor de base de dados para que possa utilizar o Serviço de Escritores SQL para implementar o Serviço de Cópia sonora do Volume Do Windows (VSS).

> [!IMPORTANT]
> O StorSimple Snapshot Manager não suporta o Escritor SharePoint VSS e não pode tirar fotografias consistentes com aplicações de dados do SharePoint. Num cenário de SharePoint, o StorSimple Snapshot Manager fornece apenas backups consistentes com o crash.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Pré-requisitos de configuração da quinta SharePoint
Certifique-se de que a sua quinta de servidores SharePoint está corretamente configurada, da seguinte forma:

* Verifique se a sua quinta de servidorsharePoint está num estado saudável e verifique o seguinte:
* Todos os servidores sharePoint WFE e de aplicações registados na quinta estão a funcionar e podem ser pingados a partir do servidor no qual estará a instalar o Adaptador StorSimple para o SharePoint.
* O serviço De tempo do SharePoint (SPTimerV3 ou SPTimerV4) está a funcionar em cada servidor e servidor de aplicações WFE.
* Tanto o serviço SharePoint Timer como o conjunto de aplicações IIS ao abrigo do qual o site da Administração Central sharePoint está em execução têm privilégios administrativos.
* Certifique-se de que o Contexto de Segurança Melhorada do Internet Explorer (IE ESC) está desativado. Siga estes passos para desativar a IE ESC:
  
  1. Feche todos os casos do Internet Explorer.
  2. Inicie o Gerente do Servidor.
  3. No painel esquerdo, clique no **Servidor Local**.
  4. No painel direito, ao lado da Configuração de **Segurança Melhorada do IE,** clique **em .**
  5. Em **Administradores,** clique **em Off**.
  6. Clique em **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Pré-requisitos de armazenamento blob remoto (RBS)
Certifique-se de que está a utilizar uma versão suportada do Servidor SQL. Apenas as seguintes versões são suportadas e capazes de utilizar o RBS:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Os BLOBs só podem ser externalizados nos volumes que o dispositivo StorSimple apresenta ao SQL Server. Não são apoiados outros objetivos para a externalização do BLOB.

Quando tiver concluído todos os passos de configuração pré-requisitos, vá instalar [o Adaptador StorSimple para O SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instale o Adaptador StorSimple para SharePoint
Utilize os seguintes passos para instalar o Adaptador StorSimple para o SharePoint. Se estiver a reinstalar o software, consulte [o Upgrade ou reinstale o Adaptador StorSimple para o SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). O tempo necessário para a instalação depende do número total de bases de dados do SharePoint na sua quinta de servidores SharePoint.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Configure RBS
Depois de instalar o Adaptador StorSimple para O SharePoint, configure o RBS conforme descrito no procedimento seguinte.

> [!TIP]
> O Adaptador StorSimple para o SharePoint liga-se à página da Administração Central do SharePoint, permitindo que o RBS seja ativado ou desativado em cada base de dados de conteúdo na exploração sharePoint. No entanto, permitir ou desativar o RBS na base de dados de conteúdos provoca um reset IIS, que, dependendo da configuração da sua quinta, pode perturbar momentaneamente a disponibilidade da extremidade frontal web do SharePoint (WFE). (Fatores como a utilização de um equilibrador de carga frontal, a carga de trabalho atual do servidor, e assim por diante, podem limitar ou eliminar esta perturbação.) Para proteger os utilizadores de uma perturbação, recomendamos que ative ou desative o RBS apenas durante uma janela de manutenção planeada.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Configure a recolha de lixo
Quando os objetos são eliminados de um site do SharePoint, não são automaticamente eliminados do volume da loja RBS. Em vez disso, um programa de manutenção de fundo assíncrono elimina bloBs órfãos da loja de ficheiros. Os administradores do sistema podem agendar este processo para funcionar periodicamente ou podem iniciá-lo sempre que necessário.

Este programa de manutenção (Microsoft.Data.Data.SqlRemoteBlobs.Maintainer.exe) é automaticamente instalado em todos os servidores e servidores de aplicações Do SharePoint WFE quando ativa rRbs. O programa está instalado no seguinte local: *boot drive*:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Keeper\

Para obter informações sobre configurar e utilizar o programa de manutenção, consulte [Manter o RBS no SharePoint Server 2013][8].

> [!IMPORTANT]
> O programa de manutenção do RBS é intensivo em recursos. Deve agendar a sua execução apenas durante períodos de atividade leve na quinta SharePoint.


### <a name="delete-orphaned-blobs-immediately"></a>Eliminar blobs órfãos imediatamente
Se necessitar de eliminar blobs órfãos imediatamente, pode utilizar as seguintes instruções. Note que estas instruções são um exemplo de como isso pode ser feito em um ambiente SharePoint 2013 com os seguintes componentes:

* O nome da base de dados de conteúdos é WSS_Content.
* O nome SQL Server é SHRPT13-SQL12\SHRPT13.
* O nome da aplicação web é SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Atualize ou reinstale o Adaptador StorSimple para SharePoint
Utilize o seguinte procedimento para atualizar o servidor SharePoint e, em seguida, reinstalar o Adaptador StorSimple para o SharePoint ou simplesmente atualizar ou reinstalar o adaptador numa exploração de servidorsharePoint existente.

> [!IMPORTANT]
> Reveja as seguintes informações antes de tentar atualizar o seu software SharePoint e/ou atualizar ou reinstalar o Adaptador StorSimple para O SharePoint:
> 
> * Quaisquer ficheiros que tenham sido previamente transferidos para armazenamento externo via RBS não estarão disponíveis até que a reinstalação esteja concluída e a funcionalidade RBS esteja novamente ativada. Para limitar o impacto do utilizador, efetue qualquer atualização ou reinstalação durante uma janela de manutenção planeada.
> * O tempo necessário para a atualização/reinstalação pode variar, dependendo do número total de bases de dados do SharePoint na exploração de servidores sharePoint.
> * Depois de concluída a atualização/reinstalação, é necessário ativar o RBS para as bases de dados de conteúdos. Consulte o [Configure RBS](#configure-rbs) para obter mais informações.
> * Se estiver a configurar o RBS para uma exploração do SharePoint que tem um número muito grande de bases de dados (superior a 200), a página **da Administração Central do SharePoint** poderá esgotar-se. Se isso ocorrer, refresque a página. Isto não afeta o processo de configuração.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>Adaptador StorSimple para remoção do SharePoint
Os seguintes procedimentos descrevem como mover os BLOBs de volta para as bases de dados de conteúdo do Servidor SQL e, em seguida, desinstalar o Adaptador StorSimple para O SharePoint. 

> [!IMPORTANT]
> Tem de transferir os BLOBs de volta para as bases de dados de conteúdos antes de desinstalar o software adaptador.


### <a name="before-you-begin"></a>Antes de começar
Recolha as seguintes informações antes de transferir os dados para as bases de dados de conteúdo do SQL Server e inicie o processo de remoção do adaptador:

* Os nomes de todas as bases de dados para as quais o RBS está habilitado
* O caminho unc da loja BLOB configurada

### <a name="move-the-blobs-back-to-the-content-databases"></a>Mover os BLOBs de volta para as bases de dados de conteúdos
Antes de desinstalar o Adaptador StorSimple para o software SharePoint, tem de migrar todos os BLOBs que foram externalizados de volta para as bases de dados de conteúdo do SQL Server. Se tentar desinstalar o Adaptador StorSimple para O SharePoint antes de transferir todos os BLOBs para as bases de dados de conteúdo, verá a seguinte mensagem de aviso.

![Mensagem de aviso](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Para mover os BLOBs de volta para as bases de dados de conteúdos
1. Descarregue cada um dos objetos externos.
2. Abra a página **da Administração Central sharePoint** e navegue para **as Definições**do Sistema .
3. Em **Azure StorSimple,** clique **em Configurar adaptador Simples**.
4. Na página **de Adapter Configure StorSimple,** clique no botão **Desativar** abaixo de cada uma das bases de dados de conteúdo que pretende remover do armazenamento BLOB externo. 
5. Elimine os objetos do SharePoint e, em seguida, carregue-os novamente.

Em alternativa, pode utilizar `RBS Migrate()` o cmdlet Microsoft PowerShell incluído com o SharePoint. Para mais informações, consulte o [conteúdo migrate para dentro ou para fora do RBS](https://technet.microsoft.com/library/ff628255.aspx).

Depois de mover os BLOBs de volta para a base de dados de conteúdos, vá para o próximo passo: [Desinstale o adaptador](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Desinstalar o adaptador
Depois de mover os BLOBs de volta para as bases de dados de conteúdo do SQL Server, utilize uma das seguintes opções para desinstalar o Adaptador StorSimple para o SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Para utilizar o programa de instalação para desinstalar o adaptador
1. Utilize uma conta com privilégios de administrador para iniciar sessão no servidor frontal web (WFE).
2. Clique duas vezes no Adaptador StorSimple para o instalador SharePoint. O Assistente de Configuração começa.
   
    ![Assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Clique em **Seguinte**. Aparece a seguinte página.
   
    ![Página de remoção de assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Clique em **Remover** para selecionar o processo de remoção. Aparece a seguinte página.
   
    ![Página de confirmação do assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Clique em **Remover** para confirmar a remoção. Aparece a seguinte página de progresso.
   
    ![Página de progresso do assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Quando a remoção estiver completa, aparece a página de chegada. Clique em **Terminar** para fechar o Assistente de Configuração.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Para utilizar o Painel de Controlo para desinstalar o adaptador
1. Abra o Painel de Controlo e, em seguida, clique em **Programas e Funcionalidades**.
2. Selecione **StorSimple Adapter para SharePoint**, e, em seguida, clique em **Desinstalar**.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
