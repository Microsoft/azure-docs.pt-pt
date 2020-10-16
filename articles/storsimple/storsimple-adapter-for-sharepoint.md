---
title: Instalar adaptador StorSimple para SharePoint / Microsoft Docs
description: Descreve como instalar e configurar ou remover o Adaptador StorSimple para SharePoint numa fazenda de servidores SharePoint.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: alkohli
ms.openlocfilehash: a33e01d68a31585e216faa51ac8af193d86c6123
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90053281"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instale e configuure o Adaptador StorSimple para SharePoint
## <a name="overview"></a>Descrição geral
O Adaptador StorSimple para o SharePoint é um componente que permite fornecer ao Microsoft Azure StorSimple armazenamento flexível e proteção de dados às explorações de servidores SharePoint. Pode utilizar o adaptador para mover o conteúdo do Objeto Grande Binário (BLOB) das bases de dados de conteúdos do SQL Server para o dispositivo de armazenamento híbrido microsoft Azure StorSimple.

O adaptador StorSimple para o SharePoint funciona como fornecedor de armazenamento blob remoto (RBS) e utiliza a função de armazenamento blob remoto do servidor SQL para armazenar conteúdo sharePoint não estruturado (sob a forma de BLOBs) num servidor de ficheiros que é apoiado por um dispositivo StorSimple.

> [!NOTE]
> O adaptador StorSimple para SharePoint suporta o SharePoint Server 2010 Remote BLOB Storage (RBS). Não suporta o SharePoint Server 2010 External BLOB Storage (EBS).


* Para descarregar o Adaptador StorSimple para o SharePoint, vá ao [Adaptador StorSimple para SharePoint][1] no Microsoft Download Center.
* Para obter informações sobre o planeamento das limitações de RBS e RBS, vá a [Decidir utilizar o RBS no SharePoint 2013][2] ou o Plano para o RBS [(SharePoint Server 2010)][3].

O resto desta visão geral descreve brevemente o papel do Adaptador StorSimple para o SharePoint e os limites de capacidade e desempenho do SharePoint que deve ter conhecimento antes de instalar e configurar o adaptador. Depois de rever estas informações, aceda ao [Adaptador StorSimple para a instalação do SharePoint](#storsimple-adapter-for-sharepoint-installation) para começar a configurar o adaptador.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>Adaptador StorSimple para benefícios sharePoint
Num site do SharePoint, o conteúdo é armazenado como dados BLOB não estruturados numa ou mais bases de dados de conteúdo. Por padrão, estas bases de dados estão hospedadas em computadores que estão a executar o SQL Server e estão localizados na fazenda de servidores SharePoint. Blobs pode aumentar rapidamente em tamanho, consumindo grandes quantidades de armazenamento no local. Por esta razão, talvez queira encontrar outra solução de armazenamento menos dispendiosa. O SQL Server fornece uma tecnologia chamada Remote Blob Storage (RBS) que permite armazenar conteúdo BLOB no sistema de ficheiros, fora da base de dados do SQL Server. Com o RBS, os BLOBs podem residir no sistema de ficheiros no computador que está a executar o SQL Server, ou podem ser armazenados no sistema de ficheiros noutro computador do servidor.

O RBS requer que utilize um fornecedor RBS, como o Adaptador StorSimple para o SharePoint, para ativar o RBS no SharePoint. O Adaptador StorSimple para SharePoint funciona com o RBS, permitindo-lhe mover BLOBs para um servidor apoiado pelo sistema Microsoft Azure StorSimple. O Microsoft Azure StorSimple armazena os dados BLOB localmente ou na nuvem, com base na utilização. BloBs que são muito ativos (tipicamente designados como Dados de Nível 1 ou dados quentes) residem localmente. Dados menos ativos e dados de arquivo residem na nuvem. Depois de ativar o RBS numa base de dados de conteúdos, qualquer novo conteúdo BLOB criado no SharePoint é armazenado no dispositivo StorSimple e não na base de dados de conteúdos.

A implementação do Microsoft Azure StorSimple do RBS proporciona os seguintes benefícios:

* Ao mover o conteúdo BLOB para um servidor separado, pode reduzir a carga de consulta no SQL Server, o que pode melhorar a capacidade de resposta do SQL Server. 
* A Azure StorSimple utiliza deduplicação e compressão para reduzir o tamanho dos dados.
* A Azure StorSimple fornece proteção de dados sob a forma de instantâneos locais e em nuvem. Além disso, se colocar a base de dados no dispositivo StorSimple, pode fazer o back up da base de dados de conteúdos e blobs de forma consistente. (A transferência da base de dados de conteúdos para o dispositivo só é suportada para o dispositivo da série StorSimple 8000. Esta funcionalidade não é suportada para a série 5000 ou 7000.)
* O Azure StorSimple inclui funcionalidades de recuperação de desastres, incluindo failover, recuperação de ficheiros e volume (incluindo recuperação de testes) e restauração rápida de dados.
* Pode utilizar software de recuperação de dados, como o Kroll Ontrack PowerControls, com imagens StorSimple de dados BLOB para realizar a recuperação ao nível do item do conteúdo do SharePoint. (Este software de recuperação de dados é uma compra separada.)
* O Adaptador StorSimple para SharePoint liga-se ao portal da Administração Central do SharePoint, permitindo-lhe gerir toda a sua solução SharePoint a partir de uma localização central.

Mover o conteúdo BLOB para o sistema de ficheiros pode proporcionar outras economias de custos e benefícios. Por exemplo, a utilização do RBS pode reduzir a necessidade de armazenamento caro do Tier 1 e, como diminui a base de dados de conteúdos, o RBS pode reduzir o número de bases de dados necessárias na fazenda de servidores SharePoint. No entanto, outros fatores, como os limites de tamanho da base de dados e a quantidade de conteúdo não-RBS, também podem afetar os requisitos de armazenamento. Para obter mais informações sobre os custos e benefícios da utilização do RBS, consulte [Plano para o RBS (SharePoint Foundation 2010)][4] e decidir utilizar o RBS no [SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Limites de capacidade e desempenho
Antes de considerar a utilização do RBS na sua solução SharePoint, deve estar ciente dos limites de desempenho e capacidade testados do SharePoint Server 2010 e sharePoint Server 2013, e de como estes limites se relacionam com um desempenho aceitável. Para mais informações, consulte [Limites e Limites de Software para SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Reveja o seguinte antes de configurar RBS:

* Certifique-se de que o tamanho total do conteúdo (o tamanho de uma base de dados de conteúdo mais o tamanho de quaisquer BLOBs externos associados) não exceda o limite de tamanho RBS suportado pelo SharePoint. Este limite é de 200 GB. 
  
    **Para medir a base de dados de conteúdos e o tamanho BLOB**
  
  1. Executar esta consulta sobre a Administração Central WFE. Inicie a Shell de Gestão sharePoint e, em seguida, introduza o seguinte comando Windows PowerShell para obter o tamanho das bases de dados de conteúdo:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Este passo obtém o tamanho da base de dados de conteúdos no disco.
  2. Executar uma das seguintes consultas SQL no SQL Management Studio na caixa de servidor SQL em cada base de dados de conteúdo, e adicione o resultado ao número obtido no passo 1.
     
     Nas bases de dados de conteúdos do SharePoint 2013, insira:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     Nas bases de dados de conteúdos do SharePoint 2010, insira:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Este passo obtém o tamanho dos BLOBs que foram externalizados.
* Recomendamos que guarde localmente todos os conteúdos BLOB e base de dados no dispositivo StorSimple. O dispositivo StorSimple é um cluster de dois nós para uma alta disponibilidade. A colocação das bases de dados de conteúdos e blobs no dispositivo StorSimple proporciona uma elevada disponibilidade.
  
    Utilize as melhores práticas de migração do SQL Server tradicionais para mover a base de dados de conteúdos para o dispositivo StorSimple. Mover a base de dados apenas depois de todo o conteúdo BLOB da base de dados ter sido transferido para a partilha de ficheiros via RBS. Se optar por mover a base de dados de conteúdos para o dispositivo StorSimple, recomendamos que configuure o armazenamento da base de dados de conteúdos no dispositivo como um volume primário.
* No Microsoft Azure StorSimple, se utilizar volumes hierárquicos, não existe forma de garantir que os conteúdos armazenados localmente no dispositivo StorSimple não serão nivelados para o armazenamento em nuvem do Microsoft Azure. Por isso, recomendamos a utilização de volumes fixados localmente em conjunto com o SharePoint RBS. Isto irá garantir que todos os conteúdos BLOB permanecem localmente no dispositivo StorSimple, e não são transferidos para o Microsoft Azure.
* Se não armazenar as bases de dados de conteúdo no dispositivo StorSimple, utilize as melhores práticas tradicionais do SQL Server que suportem o RBS. O agrupamento sql Server suporta o RBS, enquanto o espelhamento do SQL Server não. 

> [!WARNING]
> Se não tiver ativado o RBS, não recomendamos que se mova a base de dados de conteúdos para o dispositivo StorSimple. Esta é uma configuração não testada.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>Adaptador StorSimple para instalação sharePoint
Antes de poder instalar o Adaptador StorSimple para o SharePoint, tem de configurar o dispositivo StorSimple e certificar-se de que a fazenda de servidores SharePoint e a instantânea sql Server cumprem todos os requisitos. Este tutorial descreve os requisitos de configuração, bem como os procedimentos para instalar e atualizar o Adaptador StorSimple para o SharePoint.

## <a name="configure-prerequisites"></a>Configurar pré-requisitos
Antes de poder instalar o Adaptador StorSimple para o SharePoint, certifique-se de que o dispositivo StorSimple, a fazenda de servidores SharePoint e a instantiação do SQL Server cumprem os seguintes requisitos.

### <a name="system-requirements"></a>Requisitos de sistema
O adaptador StorSimple para o SharePoint funciona com o seguinte hardware e software:

* Sistema operativo suportado – Windows Server 2008 R2 SP1, Windows Server 2012 ou Windows Server 2012 R2
* Versões suportadas do SharePoint – SharePoint Server 2010 ou SharePoint Server 2013
* Versões suportadas do SQL Server – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition ou SQL Server 2012 Enterprise Edition
* Dispositivos StorSimple suportados – série StorSimple 8000, série StorSimple 7000 ou série StorSimple 5000.

### <a name="storsimple-device-configuration-prerequisites"></a>Pré-requisitos de configuração do dispositivo StorSimple
O dispositivo StorSimple é um dispositivo de bloco e, como tal, requer um servidor de ficheiros no qual os dados podem ser hospedados. Recomendamos que utilize um servidor separado em vez de um servidor existente da quinta SharePoint. Este servidor de ficheiros deve estar na mesma rede local de área (LAN) que o computador SQL Server que acolhe as bases de dados de conteúdo.

> [!TIP]
> * Se configurar a sua quinta SharePoint para uma elevada disponibilidade, deverá implementar o servidor de ficheiros para uma elevada disponibilidade também.
> * Se não armazenar a base de dados de conteúdos no dispositivo StorSimple, utilize as tradicionais boas práticas de alta disponibilidade que suportem o RBS. O agrupamento sql Server suporta o RBS, enquanto o espelhamento do SQL Server não. 


Certifique-se de que o seu dispositivo StorSimple está configurado corretamente e que os volumes adequados para suportar a sua implementação SharePoint estão configurados e acessíveis a partir do seu computador SQL Server. Vá para [implementar o seu dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md) se ainda não tiver implantado e configurado o seu dispositivo StorSimple. Note o endereço IP do dispositivo StorSimple; irá precisar dele durante o Adaptador StorSimple para a instalação do SharePoint.

Além disso, certifique-se de que o volume a utilizar para a externalização BLOB satisfaz os seguintes requisitos:

* O volume deve ser formatado com um tamanho de unidade de atribuição de 64 KB.
* A sua extremidade frontal web (WFE) e servidores de aplicações devem ser capazes de aceder ao volume através de um caminho da Convenção Universal de Nomeação (UNC).
* A quinta do servidor SharePoint deve ser configurada para escrever ao volume.

> [!NOTE]
> Depois de instalar e configurar o adaptador, toda a externalização BLOB deve passar pelo dispositivo StorSimple (o dispositivo apresentará os volumes ao SQL Server e gerirá os níveis de armazenamento). Não é possível utilizar outros alvos para a externalização blob.


Se planeia utilizar o StorSimple Snapshot Manager para tirar fotografias dos dados blob e de base de dados, certifique-se de instalar o StorSimple Snapshot Manager no servidor de base de dados para que possa utilizar o Serviço de Escritores SQL para implementar o Serviço de Cópia Sombra do Volume do Windows (VSS).

> [!IMPORTANT]
> O StorSimple Snapshot Manager não suporta o SharePoint VSS Writer e não pode tirar instantâneos consistentes de aplicações de dados do SharePoint. Num cenário sharePoint, o StorSimple Snapshot Manager fornece apenas cópias de segurança consistentes com falhas.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Pré-requisitos de configuração da fazenda SharePoint
Certifique-se de que a sua fazenda de servidores SharePoint está corretamente configurada, da seguinte forma:

* Verifique se a sua fazenda de servidores SharePoint está num estado saudável e verifique o seguinte:
* Todos os servidores do SharePoint WFE e de aplicações registados na quinta estão em execução e podem ser pingados a partir do servidor no qual irá instalar o Adaptador StorSimple para o SharePoint.
* O serviço De Temporizador SharePoint (SPTimerV3 ou SPTimerV4) está a funcionar em cada servidor e servidor de aplicações da WFE.
* Tanto o serviço De Temporizador SharePoint como o conjunto de aplicações IIS sob o qual o site da Administração Central sharePoint está em execução têm privilégios administrativos.
* Certifique-se de que o contexto de segurança melhorado do Internet Explorer (IE ESC) está desativado. Siga estes passos para desativar o IE ESC:
  
  1. Feche todas as instâncias do Internet Explorer.
  2. Inicie o Gestor do Servidor.
  3. No painel esquerdo, clique no **Servidor Local.**
  4. No painel direito, ao lado da **configuração de segurança melhorada do IE,** clique **em .**
  5. Em **Administradores**, clique **em Off**.
  6. Clique em **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Pré-requisitos de armazenamento remoto blob (RBS)
Certifique-se de que está a utilizar uma versão suportada do SQL Server. Apenas as seguintes versões são suportadas e capazes de utilizar o RBS:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

BloBs podem ser externalizados apenas nos volumes que o dispositivo StorSimple apresenta ao SQL Server. Não são apoiados outros objetivos para a externalização blob.

Quando tiver concluído todos os passos de configuração pré-requisitos, vá [instalar o Adaptador StorSimple para o SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instale o adaptador StorSimple para SharePoint
Utilize os seguintes passos para instalar o Adaptador StorSimple para o SharePoint. Se estiver a reinstalar o software, consulte [upgrade ou reinstale o Adaptador StorSimple para o SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). O tempo necessário para a instalação depende do número total de bases de dados sharePoint na sua fazenda de servidores SharePoint.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Configurar RBS
Depois de instalar o Adaptador StorSimple para o SharePoint, configure o RBS conforme descrito no seguinte procedimento.

> [!TIP]
> O Adaptador StorSimple para SharePoint liga-se à página da Administração Central do SharePoint, permitindo que o RBS seja ativado ou desativado em cada base de dados de conteúdos na quinta SharePoint. No entanto, ativar ou desativar o RBS na base de dados de conteúdos provoca um reset do IIS, o que, dependendo da configuração da sua quinta, pode perturbar momentaneamente a disponibilidade da extremidade frontal do SharePoint (WFE). (Fatores como a utilização de um balançador de carga frontal, a carga de trabalho do servidor atual, e assim por diante, podem limitar ou eliminar esta perturbação.) Para proteger os utilizadores de uma perturbação, recomendamos que ative ou desative o RBS apenas durante uma janela de manutenção planeada.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Recolha de lixo configure
Quando os objetos são eliminados de um site do SharePoint, não são automaticamente eliminados do volume da loja RBS. Em vez disso, um programa de manutenção de fundo assíncronos elimina blobs órfãos da loja de ficheiros. Os administradores do sistema podem agendar este processo periodicamente ou podem iniciá-lo sempre que necessário.

Este programa de manutenção (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) é instalado automaticamente em todos os servidores e servidores de aplicações do SharePoint WFE quando ativa o RBS. O programa está instalado no seguinte local: *boot drive*:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Keeper\

Para obter informações sobre a configuração e utilização do programa de manutenção, consulte [Manter o RBS no SharePoint Server 2013][8].

> [!IMPORTANT]
> O programa de manutenção RBS é intensivo em recursos. Deverá agendar para funcionar apenas durante períodos de atividade leve na quinta SharePoint.


### <a name="delete-orphaned-blobs-immediately"></a>Eliminar blobs órfãos imediatamente
Se precisar de eliminar blobs órfãos imediatamente, pode utilizar as seguintes instruções. Note que estas instruções são um exemplo de como isto pode ser feito num ambiente SharePoint 2013 com os seguintes componentes:

* O nome da base de dados de conteúdos é WSS_Content.
* O nome sql Server é SHRPT13-SQL12\SHRPT13.
* O nome da aplicação web é SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Atualizar ou reinstalar o Adaptador StorSimple para SharePoint
Utilize o seguinte procedimento para atualizar o servidor SharePoint e, em seguida, reinstalar o Adaptador StorSimple para o SharePoint ou simplesmente atualizar ou reinstalar o adaptador numa fazenda de servidores SharePoint existente.

> [!IMPORTANT]
> Reveja as seguintes informações antes de tentar atualizar o seu software SharePoint e/ou atualizar ou reinstalar o Adaptador StorSimple para o SharePoint:
> 
> * Quaisquer ficheiros que foram previamente transferidos para armazenamento externo via RBS não estarão disponíveis até que a reinstalação esteja concluída e a funcionalidade RBS esteja novamente ativada. Para limitar o impacto do utilizador, efetue qualquer atualização ou reinstalação durante uma janela de manutenção planeada.
> * O tempo necessário para a atualização/reinstalação pode variar, dependendo do número total de bases de dados sharePoint na fazenda de servidores SharePoint.
> * Após a atualização/reinstalação estar concluída, é necessário ativar o RBS para as bases de dados de conteúdos. Consulte [o Configurar RBS](#configure-rbs) para obter mais informações.
> * Se estiver a configurar o RBS para uma quinta SharePoint que tenha um número muito grande de bases de dados (superior a 200), a página **da Administração Central do SharePoint** pode sair. Se isso ocorrer, refresque a página. Isto não afeta o processo de configuração.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>Adaptador StorSimple para remoção do SharePoint
Os seguintes procedimentos descrevem como mover os BLOBs de volta para as bases de dados de conteúdos do SQL Server e, em seguida, desinstalar o Adaptador StorSimple para SharePoint. 

> [!IMPORTANT]
> Tem de deslocar os BLOBs de volta para as bases de dados de conteúdos antes de desinstalar o software do adaptador.


### <a name="before-you-begin"></a>Antes de começar
Recolher as seguintes informações antes de transferir os dados para as bases de dados de conteúdos do SQL Server e iniciar o processo de remoção do adaptador:

* Os nomes de todas as bases de dados para as quais o RBS está habilitado
* O caminho da UNC da loja BLOB configurada

### <a name="move-the-blobs-back-to-the-content-databases"></a>Mova os BLOBs de volta para as bases de dados de conteúdos
Antes de desinstalar o Adaptador StorSimple para o software SharePoint, tem de migrar todos os BLOBs que foram externalizados de volta para as bases de dados de conteúdos do SQL Server. Se tentar desinstalar o Adaptador StorSimple para o SharePoint antes de transferir todos os BLOBs para as bases de dados de conteúdos, verá a seguinte mensagem de aviso.

![Mensagem de aviso](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Para mover os BLOBs de volta para as bases de dados de conteúdos
1. Descarregue cada um dos objetos externos.
2. Abra a página da **Administração Central do SharePoint** e navegue para **definições do sistema**.
3. No **Azure StorSimple,** clique **no Adaptador Configurar StorSimple**.
4. Na página **Configure StorSimple Adapter,** clique no botão **Desativar** abaixo de cada uma das bases de dados de conteúdo que pretende remover do armazenamento externo blob. 
5. Elimine os objetos do SharePoint e, em seguida, faça o upload novamente.

Em alternativa, pode utilizar o `RBS Migrate()` cmdlet Microsoft PowerShell incluído no SharePoint. Para obter mais informações, consulte [o conteúdo da Migração dentro ou fora do RBS](https://technet.microsoft.com/library/ff628255.aspx).

Depois de deslocar os BLOBs de volta para a base de dados de conteúdos, vá para o passo seguinte: [Desinstalar o adaptador](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Desinstalar o adaptador
Depois de transferir os BLOBs para as bases de dados de conteúdos do SQL Server, utilize uma das seguintes opções para desinstalar o Adaptador StorSimple para o SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Para utilizar o programa de instalação para desinstalar o adaptador
1. Utilize uma conta com privilégios de administrador para iniciar sessão no servidor frontal (WFE) da web.
2. Clique duas vezes no Adaptador StorSimple para instalador SharePoint. O Assistente de Configuração começa.
   
    ![Assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Clique em **Seguinte**. Aparece a seguinte página.
   
    ![Página de remoção do assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Clique **em Remover** para selecionar o processo de remoção. Aparece a seguinte página.
   
    ![Página de confirmação do assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Clique **em Remover** para confirmar a remoção. Aparece a seguinte página de progresso.
   
    ![Página de progresso do assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Quando a remoção estiver completa, a página de chegada aparece. Clique **em Terminar** para fechar o Assistente de Configuração.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Para utilizar o Painel de Controlo para desinstalar o adaptador
1. Abra o Painel de Controlo e, em seguida, clique em **Programas e Funcionalidades**.
2. Selecione **Adaptador StorSimple para SharePoint**e, em seguida, clique em **Desinstalar**.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre o StorSimple.](storsimple-overview.md)

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/library/ff943565.aspx
