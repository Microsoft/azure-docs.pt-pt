---
title: Provisionar máquina virtual com portal do Azure
description: Este guia de instruções descreve as opções para criar máquinas virtuais do Windows SQL Server 2017 no portal do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 1fdf776570b6f10a363fb98dfe343387d86219d6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75350924"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Como provisionar uma máquina virtual do Windows SQL Server no portal do Azure

Este guia fornece detalhes sobre as diferentes opções disponíveis quando você cria uma máquina virtual do Windows SQL Server no portal do Azure. Este artigo aborda mais opções de configuração do que o guia de [início rápido da VM SQL Server](quickstart-sql-vm-create-portal.md), que passa mais por uma possível tarefa de provisionamento. 

Use este guia para criar sua própria VM SQL Server. Ou use-o como uma referência para as opções disponíveis no portal do Azure.

> [!TIP]
> Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a id="select"></a>SQL Server imagens da Galeria de máquinas virtuais

Ao criar uma máquina virtual SQL Server, você pode selecionar uma das várias imagens pré-configuradas da Galeria de máquinas virtuais. As etapas a seguir demonstram como selecionar uma das SQL Server imagens 2017.

1. Selecione **SQL do Azure** no menu à esquerda da portal do Azure. Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. Adicional Selecione a estrela ao lado de **Azure SQL** para que ela seja favorita e adicione-a como um item no painel de navegação à esquerda. 
1. Selecione **+ Adicionar** para abrir a página **selecionar opção de implantação do SQL** . Você pode exibir informações adicionais selecionando **Mostrar detalhes**. 
1. Digite `2017` na caixa de pesquisa de imagem de SQL Server no bloco de **máquinas virtuais do SQL** e selecione **licença de SQL Server gratuita: SQL Server Developer 2017 no Windows Server 2016** na lista suspensa. 


   ![Selecionar imagem da VM do SQL](media/virtual-machines-windows-portal-sql-server-provision/select-sql-vm-image-portal.png)

   > [!TIP]
   > A edição Developer é usada neste passo a passos porque é uma edição completa e gratuita do SQL Server para testes de desenvolvimento. Apenas paga pelo custo de execução da VM. No entanto, você tem a liberdade de escolher qualquer uma das imagens a serem usadas nesta explicação. Para obter uma descrição das imagens disponíveis, consulte a [visão geral de SQL Server máquinas virtuais do Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Os custos de licenciamento para SQL Server são incorporados ao preço por segundo da VM que você cria e varia por edição e núcleos. No entanto, a edição SQL Server Developer é gratuita para desenvolvimento/teste (não para produção) e o SQL Express é gratuito para cargas de trabalho leves (menos de 1 GB de memória, menos de 10 GB de armazenamento). Você também pode trazer sua própria licença (BYOL) e pagar apenas pela VM. Aos nomes dessas imagem é adicionado o prefixo {BYOL}. 
   >
   > Para obter mais informações sobre estas opções, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).


1. Selecione **Criar**.


## <a name="1-configure-basic-settings"></a>1. definir configurações básicas


Na guia **noções básicas** , forneça as seguintes informações:

* Em **detalhes do projeto**, verifique se a assinatura correta está selecionada. 
*  Na seção **grupo de recursos** , selecione um grupo de recursos existente na lista ou escolha **criar novo** para criar um novo grupo de recursos. Um grupo de recursos é uma coleção de recursos relacionados no Azure (máquinas virtuais, contas do Storage, redes virtuais, etc.). 

    ![Subscrição](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Utilizar um novo grupo de recursos é útil se estiver apenas a testar ou a saber mais sobre implementações do SQL Server no Azure. Depois de terminar o teste, elimine o grupo de recursos para eliminar automaticamente a VM e todos os recursos associados a esse grupo de recursos. Para mais informações sobre grupos de recursos, consulte o artigo [Descrição Geral do Azure Resource Manager](../../../azure-resource-manager/management/overview.md).


* Em **detalhes da instância**:
    1. Insira um **nome de máquina virtual**exclusivo.  
    1. Escolha um local para a sua **região**. 
    1. Para a finalidade deste guia, deixe **as opções de disponibilidade** definidas como _nenhuma redundância de infraestrutura necessária_. Para obter mais informações sobre as opções de disponibilidade, consulte [disponibilidade](../../windows/availability.md). 
    1. Na lista de **imagens** , selecione _licença de SQL Server gratuita: desenvolvedor de SQL Server 2017 no Windows Server 2016_.  
    1. Escolha **alterar o tamanho** do **tamanho** da máquina virtual e selecione a oferta **básica a2** . Certifique-se de limpar seus recursos depois de concluí-los para evitar cobranças inesperadas. Relativamente a cargas de trabalho de produção, veja os tamanhos e as configurações de máquinas recomendados em [Performance best practices for SQL Server in Azure Virtual Machine](virtual-machines-windows-sql-performance.md) (Melhores práticas de desempenho para o SQL Server nas Máquinas Virtuais do Azure).

    ![Detalhes da instância](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> O custo mensal estimado apresentado na janela **Escolher um tamanho** não inclui os custos de licenciamento do SQL Server. Essa estimativa é o custo da VM sozinha. Para as edições Express e Developer do SQL Server, essa estimativa é o custo estimado total. Quanto a outras edições, veja a [página de preços das Máquinas Virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e selecione a edição de destino do SQL Server. Consulte também as [diretrizes de preços para SQL Server VMs](virtual-machines-windows-sql-server-pricing-guidance.md) e [tamanhos do Azure para máquinas virtuais](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Em **conta de administrador**, forneça um nome de usuário e uma senha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Conta de administrador](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* Em **regras de porta de entrada**, escolha **permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** na lista suspensa. 

   ![Regras da porta de entrada](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. configurar recursos opcionais

### <a name="disks"></a>Discos

Na guia **discos** , configure as opções de disco. 

* Em **tipo de disco do so**, selecione o tipo de disco desejado para o seu sistema operacional na lista suspensa. O Premium é recomendado para sistemas de produção, mas não está disponível para uma VM básica. Para utilizar SSD Premium, altere o tamanho da máquina virtual. 
* Em **avançado**, selecione **sim** em usar **Managed disks**.

   > [!NOTE]
   > A Microsoft recomenda o Managed Disks para o SQL Server. O Managed Disks processa o armazenamento em segundo plano. Além disso, se houver máquinas virtuais com Managed Disks no mesmo conjunto de disponibilidade, o Azure distribui os recursos de armazenamento para fornecer a redundância adequada. Para obter mais informações, consulte [visão geral do Azure Managed disks](../managed-disks-overview.md). Para obter informações específicas sobre os discos geridos em conjuntos de disponibilidade, veja [Use managed disks for VMs in availability set](../manage-availability.md) (Utilizar discos geridos em VMs num conjunto de disponibilidade).

![Configurações de disco da VM do SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Funcionamento em Rede

Na guia **rede** , configure as opções de rede. 

* Crie uma nova **rede virtual**ou use uma vNet existente para sua VM SQL Server. Designe uma **sub-rede** também. 

* Em **grupo de segurança de rede NIC**, selecione um grupo de segurança básico ou o grupo de segurança avançado. Escolher a opção básico permite que você selecione portas de entrada para a VM SQL Server (os mesmos valores que foram configurados na guia **básico** ). A seleção da opção avançado permite que você escolha um grupo de segurança de rede existente ou crie um novo. 

* Você pode fazer outras alterações nas configurações de rede ou manter os valores padrão.

![Configurações de rede da VM do SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitorização

Na guia **monitoramento** , configure o monitoramento e o desligamento automático. 

* O Azure habilita o **diagnóstico de inicialização** por padrão com a mesma conta de armazenamento designada para a VM. Você pode alterar essas configurações aqui, bem como habilitar o **diagnóstico de convidado do sistema operacional**. 
* Você também pode habilitar a **identidade gerenciada atribuída pelo sistema** e o **desligamento** automático nessa guia. 

![Configurações de gerenciamento de VM do SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. definir configurações de SQL Server

Na guia **configurações de SQL Server** , defina configurações e otimizações específicas para SQL Server. As configurações que você pode definir para SQL Server incluem o seguinte:

| Definição |
| --- |
| [Conetividade](#connectivity) |
| [Autenticação](#authentication) |
| [Integração do Cofre de Chaves do Azure](#azure-key-vault-integration) |
| [Configuração do armazenamento](#storage-configuration) |
| [Aplicação de Patches Automatizada](#automated-patching) |
| [Cópia de Segurança Automatizada](#automated-backup) |
| [Serviços de Machine Learning](#machine-learning-services) |


### <a name="connectivity"></a>Ligação

Em **Conectividade do SQL**, especifique o tipo de acesso que pretende para a instância do SQL Server nesta VM. Para os fins deste passo a passos, selecione **público (Internet)** para permitir conexões a SQL Server de computadores ou serviços na Internet. Com essa opção selecionada, o Azure configura automaticamente o firewall e o grupo de segurança de rede para permitir o tráfego na porta selecionada.

> [!TIP]
> Por predefinição, o SQL Server escuta numa porta bem conhecida, a **1433**. Para maior segurança, altere a porta na caixa de diálogo anterior para escutar numa porta não predefinida, como 1401. Se você alterar a porta, deverá se conectar usando essa porta de qualquer ferramenta de cliente, como o SSMS.

![Segurança da VM do SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Para ligar ao SQL Server através da Internet, também tem de ativar a Autenticação do SQL Server, que se descreve na secção seguinte.

Se preferir não ativar ligações para o Motor de Base de dados através da Internet, escolha uma das seguintes opções:

* **Local (no interior da VM)** para permitir ligações ao SQL Server, apenas a partir de dentro da VM.
* **Privada (na Virtual Network)** para permitir ligações ao SQL Server a partir de máquinas ou serviços na mesma rede virtual.

Em geral, melhore a segurança, selecionando a conectividade mais restritiva que permite o seu cenário. Mas todas as opções têm capacidade de segurança através das regras do Grupo de Segurança de Rede e Autenticação do SQL/Windows. Pode editar o Grupo de Segurança de Rede após a VM ter sido criada. Para obter mais informações, veja [Security Considerations for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-security.md) (Considerações de segurança para o SQL Server em Máquinas Virtuais do Azure).



### <a name="authentication"></a>Autenticação

Se você precisar de SQL Server autenticação, clique em **habilitar** em **autenticação SQL** na guia **configurações de SQL Server** .

![Autenticação do SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Se você planeja acessar SQL Server pela Internet (a opção de conectividade pública), você deve habilitar a autenticação do SQL aqui. O acesso público ao SQL Server requer a utilização da Autenticação do SQL.

Se ativar a Autenticação do SQL Server, especifique um **Nome de início de sessão** e uma **Palavra-passe**. Esse nome de logon é configurado como um logon de autenticação SQL Server e membro da função de servidor fixa **sysadmin** . Para obter mais informações sobre Modos de Autenticação, veja [Choose an Authentication Mode](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) (Escolher um Modo de Autenticação).

Se não ativar a Autenticação do SQL Server, em seguida, pode utilizar a conta de Administrador local na VM para ligar à instância do SQL Server.


### <a name="azure-key-vault-integration"></a>Integração do Cofre de Chaves do Azure

Para armazenar segredos de segurança no Azure para criptografia, selecione **configurações de SQL Server**e role para baixo até **integração do cofre de chaves do Azure**. Selecione **habilitar** e preencha as informações solicitadas. 

![Integração do Cofre de Chaves do Azure](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

A tabela seguinte lista os parâmetros necessários para configurar a Integração do Cofre de Chaves do Azure.

| PARÂMETRO | DESCRIÇÃO | EXEMPLO |
| --- | --- | --- |
| **URL do Cofre de Chaves** |A localização do cofre de chaves. |https:\//contosokeyvault.vault.azure.net/ |
| **Nome principal** |Nome principal do serviço Azure Active Directory. Este nome também é referido como o ID de Cliente. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Segredo principal** |Segredo principal do serviço Azure Active Directory. Este segredo também é referido como o Segredo do Cliente. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Nome da credencial** |**Nome da credencial**: a Integração AKV cria uma credencial dentro do SQL Server, permitindo que a VM tenha acesso ao cofre de chaves. Escolha um nome para esta credencial. |mycred1 |

Para obter mais informações, consulte o artigo [Configurar a Integração do Cofre de Chaves do Azure para o SQL Server em VMs do Azure](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="storage-configuration"></a>Configuração do armazenamento

Na guia **configurações de SQL Server** , em **configuração de armazenamento**, selecione **Alterar configuração** para abrir a página configuração de armazenamento otimizado para desempenho e especifique os requisitos de armazenamento.

![Configuração de armazenamento da VM do SQL](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

Em **Armazenamento otimizado para**, selecione uma das seguintes opções:

* **Geral** é a predefinição e suporta a maioria das cargas de trabalho.
* O **processamento transacional** otimiza o armazenamento para cargas de trabalho OLTP tradicionais do banco de dados.
* O **Armazenamento de dados** otimiza o armazenamento para cargas de trabalho analíticas e de relatórios.

![Configuração de armazenamento da VM do SQL](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

Você pode optar por deixar os valores em padrão ou pode alterar manualmente a topologia de armazenamento para atender às suas necessidades de IOPS. Para obter mais informações, consulte [configuração de armazenamento](virtual-machines-windows-sql-server-storage-configuration.md). 

### <a name="sql-server-license"></a>Licença de SQL Server
Se você for um cliente do Software Assurance, poderá utilizar o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para trazer sua própria licença de SQL Server e salvar os recursos. 

![Licença da VM do SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Aplicação de patches automatizada

A **Aplicação de patches automatizada** está ativada por predefinição. A Aplicação de patches automatizada permite ao Azure automaticamente aplicar o patch do SQL Server e o sistema operativo. Especifique um dia da semana, a hora e a duração de uma janela de manutenção. O Azure executa a aplicação de patches nesta janela de manutenção. A agenda da janela de manutenção utiliza a região da VM para a hora. Se não pretender que o Azure aplique automaticamente o patch do SQL Server e o sistema operativo, clique em **Desativar**.  

![Aplicação de patch automatizada da VM do SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

Para obter mais informações, consulte o artigo [Aplicação de Patches Automatizada para o SQL Server nas Virtual Machines do Azure](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Cópia de segurança automatizada

Ative as cópias de segurança de bases de dados automáticas em **Cópia de segurança automatizada**. A cópia de segurança automatizada está desativada por predefinição.

Quando ativa a cópia de segurança automatizada do SQL, pode configurar as seguintes definições:

* Período de retenção (dias) para cópias de segurança
* Conta do Storage a utilizar para as cópias de segurança
* Opção de encriptação e palavra-passe para as cópias de segurança
* Realizar cópia de segurança das bases de dados do sistema
* Configurar agenda da cópia de segurança

Para encriptar a cópia de segurança, clique em **Ativar**. Em seguida, especifique a **Palavra-passe**. O Azure cria um certificado para encriptar as cópias de segurança e utiliza a palavra-passe especificada para proteger o certificado. Por padrão, a agenda é definida automaticamente, mas você pode criar uma agenda manual selecionando **manual**. 

![Backups automatizados da VM do SQL](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

Para obter mais informações, consulte o artigo [Cópia de Segurança Automatizada para o SQL Server nas Virtual Machines do Azure](virtual-machines-windows-sql-automated-backup.md).


### <a name="machine-learning-services"></a>Serviços do Machine Learning

Você tem a opção de habilitar [serviços de Machine Learning](/sql/advanced-analytics/). Essa opção permite que você use o Machine Learning com Python e R no SQL Server 2017. Selecione **habilitar** na janela **configurações de SQL Server** .


## <a name="4-review--create"></a>4. revisar + criar

Na guia **revisar + criar** , examine o resumo e selecione **criar** para criar SQL Server, grupo de recursos e recursos especificados para essa VM.

Pode monitorizar a implementação a partir do portal do Azure. O botão **Notificações** na parte superior do ecrã mostra o estado básico da implementação.

> [!NOTE]
> Para lhe fornecer uma ideia dos tempos da implementação, implementei uma VM do SQL para a região EUA Leste com as predefinições. Esta implementação de teste demorou aproximadamente 12 minutos a concluir. Mas poderá ter uma implementação mais lenta ou mais rápida com base na sua região e nas definições selecionadas.

## <a id="remotedesktop"></a> Abrir a VM com o Ambiente de Trabalho Remoto

Utilize os seguintes passos para ligar à máquina virtual do SQL Server com o Ambiente de Trabalho Remoto:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Depois de ligar à máquina virtual do SQL Server, pode iniciar o SQL Server Management Studio e estabelecer ligação à Autenticação do Windows com as suas credenciais de administrador local. Se ativou a Autenticação do SQL Server, também pode ligar com Autenticação do SQL Server com o início de sessão do SQL e a palavra-passe que configurou durante o aprovisionamento.

O acesso ao computador permite-lhe alterar diretamente as definições do SQL Server e da máquina com base nos seus requisitos. Por exemplo, pode configurar as definições da firewall ou alterar as definições de configuração do SQL Server.

## <a id="connect"></a> Ligar ao SQL Server remotamente

Neste tutorial, você selecionou acesso **público** para a máquina virtual e **SQL Server autenticação**. Estas definições configuraram automaticamente a máquina virtual para permitir ligações ao SQL Server a partir de qualquer cliente através da Internet (partindo do princípio de que tem o início de sessão SQL correto).

> [!NOTE]
> Se não selecionou Público durante o aprovisionamento, pode alterar as definições de conectividade SQL através do portal após o aprovisionamento. Para obter mais informações, veja o artigo [Alterar as definições de conectividade SQL](virtual-machines-windows-sql-connect.md#change).

As seções a seguir mostram como se conectar pela Internet à sua instância de VM SQL Server.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > Este exemplo usa a porta comum 1433. No entanto, esse valor precisará ser modificado se uma porta diferente (como 1401) tiver sido especificada durante a implantação da VM de SQL Server. 


## <a name="next-steps"></a>Passos seguintes

Para outras informações sobre como utilizar o SQL Server no Azure, consulte o artigo [SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-server-iaas-overview.md) e as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).
