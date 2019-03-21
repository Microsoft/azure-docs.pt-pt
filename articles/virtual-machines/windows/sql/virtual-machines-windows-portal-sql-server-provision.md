---
title: Aprovisionamento de guia para VMs do SQL Server do Windows no portal do Azure | Documentos da Microsoft
description: Este guia de procedimentos descreve as opções para a criação de máquinas de virtuais do Windows SQL Server 2017 no portal do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 05/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ee6c05b2da4347ed034ae6e7318d6f5d90ea7884
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099649"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>Como aprovisionar uma máquina virtual do Windows SQL Server no portal do Azure

Este guia fornece detalhes sobre as diferentes opções disponíveis quando cria uma máquina virtual Windows SQL Server no portal do Azure. Este artigo abrange mais opções de configuração que o [início rápido de VM do SQL Server](quickstart-sql-vm-create-portal.md), que passa mais por uma possível tarefa de aprovisionamento. 

Utilize este guia para criar sua própria VM do SQL Server. Em alternativa, utilize-o como uma referência para as opções disponíveis no portal do Azure.

> [!TIP]
> Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a id="select"></a> Imagens de Galeria de máquinas virtuais do SQL Server

Quando cria uma máquina virtual do SQL Server, pode selecionar uma das várias imagens pré-configuradas a partir da galeria da máquina virtual. Os passos seguintes demonstram como selecionar uma das imagens SQL Server 2017.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com) utilizando a sua conta.

1. No portal do Azure, clique em **Criar um recurso**. O portal abre a janela **Novo**.

1. Na janela **Novo**, clique em **Computação** e, em seguida, clique em **Ver todos**.

   ![Janela Nova Computação](./media/virtual-machines-windows-portal-sql-server-provision/azure-new-compute-blade.png)

1. No campo de pesquisa, escreva **SQL Server 2017** e prima ENTER.

1. Em seguida, clique no ícone **Filtro**.

1. Na janela Filtro, assinale a subcategoria **Baseado no Windows** e **Microsoft** para o publicador. Em seguida, clique em **Concluído** para filtrar os resultados para imagens do SQL Server do Windows publicadas pela Microsoft.

   ![Janela Máquinas Virtuais do Azure](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade2.png)

1. Reveja as imagens do SQL Server disponíveis. Cada imagem identifica uma versão do SQL Server e um sistema operativo.

1. Selecione a imagem com o nome **licença gratuita do SQL Server: O SQL Server 2017 Developer no Windows Server 2016**.

   > [!TIP]
   > A edição de programador é utilizada nestas instruções porque é uma edição completa, gratuita do SQL Server para teste de desenvolvimento. Apenas paga pelo custo de execução da VM. No entanto, é livre para escolher qualquer uma das imagens para utilizar esta explicação passo a passo. Para obter uma descrição de imagens disponíveis, consulte a [descrição geral de máquinas virtuais do SQL Server Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Custos de licenciamento para o SQL Server estão incorporados nos preços da VM, criar e varia por segundo, edição e núcleos. No entanto, edição do SQL Server Developer é gratuita para programação/teste (não produção) e o SQL Express é gratuito para cargas de trabalho leves (inferiores a 1 GB de memória inferior a 10 GB de armazenamento). Também pode trazer-sua própria licença (BYOL) e pagar apenas para a VM. Aos nomes dessas imagem é adicionado o prefixo {BYOL}. 
   >
   > Para obter mais informações sobre estas opções, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).

1. Em **Selecionar um modelo de implementação**, confirme se **Resource Manager** está selecionado. O modelo de implementação recomendado para máquinas virtuais novas é o Resource Manager. 

1. Clique em **Criar**.

    ![Criar a VM do SQL com o Resource Manager](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a id="configure"></a> Opções de configuração
Existem cinco janelas para configurar uma máquina virtual do SQL Server.

| Passo | Descrição |
| --- | --- |
| **Noções básicas** |[Configurar as definições básicas](#1-configure-basic-settings) |
| **Tamanho** |[Selecionar o tamanho da máquina virtual](#2-choose-virtual-machine-size) |
| **Definições** |[Configurar funcionalidades opcionais](#3-configure-optional-features) |
| **Definições do SQL Server** |[Configurar definições do SQL Server](#4-configure-sql-server-settings) |
| **Resumo** |[Reveja o resumo](#5-review-the-summary) |

## <a name="1-configure-basic-settings"></a>1. Configurar as definições básicas

Na janela **Informações Básicas**, forneça as seguintes informações:

* Introduza um **Nome** exclusivo para a máquina virtual.

* Selecione **SSD** no tipo de disco da VM, para um desempenho ideal.

* Especifique um **Nome de utilizador** para a conta de administrador local no VM. Esta conta também é adicionada à função de servidor fixa **sysadmin** do SQL Server.

* Forneça uma **Palavra-passe** forte.

* Se tiver várias subscrições, certifique-se de que a subscrição está correta para a nova VM.

* Na caixa **Grupo de recursos**, escreva um nome para um novo grupo de recursos. Em alternativa, para utilizar um grupo de recursos existente clique em **Utilizar existente**. Um grupo de recursos é uma coleção de recursos relacionados no Azure (máquinas virtuais, contas do Storage, redes virtuais, etc.).

  > [!NOTE]
  > Utilizar um novo grupo de recursos é útil se estiver apenas a testar ou a saber mais sobre implementações do SQL Server no Azure. Depois de terminar o teste, elimine o grupo de recursos para eliminar automaticamente a VM e todos os recursos associados a esse grupo de recursos. Para mais informações sobre grupos de recursos, consulte o artigo [Descrição Geral do Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md).

* Selecione um **localização** para a região do Azure para alojar esta implementação.

* Clique em **OK** para guardar as definições.

    ![Janela Informações Básicas do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. Selecionar o tamanho da máquina virtual

No passo **Tamanho**, escolha um tamanho de máquina virtual na janela **Escolher um tamanho**. Inicialmente, a janela apresenta os tamanhos de máquina recomendados com base na imagem que selecionou.

> [!IMPORTANT]
> O custo mensal estimado apresentado na janela **Escolher um tamanho** não inclui os custos de licenciamento do SQL Server. Esta estimativa é o custo da VM autónoma. Para as edições Express e Developer do SQL Server, esta estimativa é o custo total estimado. Quanto a outras edições, veja a [página de preços das Máquinas Virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e selecione a edição de destino do SQL Server. Consulte também [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).

![Opções de Tamanho da VM do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

Relativamente a cargas de trabalho de produção, veja os tamanhos e as configurações de máquinas recomendados em [Performance best practices for SQL Server in Azure Virtual Machine](virtual-machines-windows-sql-performance.md) (Melhores práticas de desempenho para o SQL Server nas Máquinas Virtuais do Azure).

> [!NOTE]
> Para obter mais informações sobre os tamanhos da máquina virtual, veja [Sizes for virtual machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Tamanhos das máquinas virtuais).

Selecione o tamanho da máquina e, em seguida, clique em **Selecionar**.

## <a name="3-configure-optional-features"></a>3. Configurar funcionalidades opcionais

Na janela **Definições**, configure o armazenamento, as redes e a monitorização do Azure para a máquina virtual.

* Em **Armazenamento**, selecione **Sim**, em **Managed Disks**.

   > [!NOTE]
   > A Microsoft recomenda o Managed Disks para o SQL Server. O Managed Disks processa o armazenamento em segundo plano. Além disso, se houver máquinas virtuais com Managed Disks no mesmo conjunto de disponibilidade, o Azure distribui os recursos de armazenamento para fornecer a redundância adequada. Para obter mais informações, consulte [Managed Disks descrição geral do Azure] [... / geridos-discos-overview.md). Para obter informações específicas sobre os discos geridos em conjuntos de disponibilidade, veja [Use managed disks for VMs in availability set](../manage-availability.md) (Utilizar discos geridos em VMs num conjunto de disponibilidade).

* Sob **rede**, selecione qualquer entrada portas que no **Selecione as portas de entrada públicas** lista. Por exemplo, se pretender que a área de trabalho remota na VM, selecione o **RDP (. 3389)** porta.

   ![Portas de entrada](./media/quickstart-sql-vm-create-portal/inbound-ports.png)

   > [!NOTE]
   > Pode selecionar a porta **MS SQL (1433)** para aceder remotamente ao SQL Server. No entanto, isso não é necessário aqui, porque o **definições do SQL Server** etapa fornece esta opção também. Se selecionar a porta 1433 neste passo, esta será aberta independentemente das suas seleções no passo **Definições do SQL Server**.

   Pode fazer outras alterações às definições de rede, ou mantenha os valores predefinidos.

* O Azure ativa a **Monitorização** por predefinição com a mesma conta do Storage designada para a VM. Pode alterar estas definições aqui.

* Sob **conjunto de disponibilidade**, pode deixar a predefinição **nenhum** nestas instruções. Se planear configurar Grupos de Disponibilidade AlwaysOn do SQL Server, configure a disponibilidade para evitar recriar a máquina virtual.  Para obter mais informações, consulte o artigo [Gerir a Disponibilidade das Virtual Machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Quando tiver terminado de configurar estas definições, clique em **OK**.

## <a name="4-configure-sql-server-settings"></a>4. Configurar definições do SQL Server
Na janela **Definições do SQL Server**, configure definições e otimizações específicas do SQL Server. As definições que pode configurar para o SQL Server incluem o seguinte.

| Definição |
| --- |
| [Conetividade](#connectivity) |
| [Autenticação](#authentication) |
| [Configuração do armazenamento](#storage-configuration) |
| [Aplicação de Patches Automatizada](#automated-patching) |
| [Cópia de Segurança Automatizada](#automated-backup) |
| [Integração do Cofre de Chaves do Azure](#azure-key-vault-integration) |
| [Serviços de Machine Learning do SQL Server](#sql-server-machine-learning-services) |

### <a name="connectivity"></a>Conectividade

Em **Conectividade do SQL**, especifique o tipo de acesso que pretende para a instância do SQL Server nesta VM. Para efeitos nestas instruções, selecione **público (internet)** para permitir ligações ao SQL Server a partir de máquinas ou serviços na internet. Com esta opção selecionada, o Azure configura automaticamente a firewall e o grupo de segurança de rede para permitir tráfego na porta 1433.

![Opções de Conectividade do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

> [!TIP]
> Por predefinição, o SQL Server escuta numa porta bem conhecida, a **1433**. Para maior segurança, altere a porta na caixa de diálogo anterior para escutar numa porta não predefinida, como 1401. Se alterar a porta, tem de ligar com essa porta qualquer das ferramentas de cliente, como o SSMS.

Para ligar ao SQL Server através da Internet, também tem de ativar a Autenticação do SQL Server, que se descreve na secção seguinte.

Se preferir não ativar ligações para o Motor de Base de dados através da Internet, escolha uma das seguintes opções:

* **Local (no interior da VM)** para permitir ligações ao SQL Server, apenas a partir de dentro da VM.
* **Privada (na Virtual Network)** para permitir ligações ao SQL Server a partir de máquinas ou serviços na mesma rede virtual.

Em geral, melhore a segurança, selecionando a conectividade mais restritiva que permite o seu cenário. Mas todas as opções têm capacidade de segurança através das regras do Grupo de Segurança de Rede e Autenticação do SQL/Windows. Pode editar o Grupo de Segurança de Rede após a VM ter sido criada. Para obter mais informações, veja [Security Considerations for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-security.md) (Considerações de segurança para o SQL Server em Máquinas Virtuais do Azure).

### <a name="authentication"></a>Authentication

Se necessitar da Autenticação do SQL Server, clique em **Ativar** em **Autenticação do SQL**.

![Autenticação do SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

> [!NOTE]
> Se pretender aceder ao SQL Server através da internet (a opção de conectividade pública), tem de ativar autenticação do SQL aqui. O acesso público ao SQL Server requer a utilização da Autenticação do SQL.

Se ativar a Autenticação do SQL Server, especifique um **Nome de início de sessão** e uma **Palavra-passe**. Este nome de utilizador está configurado como um início de sessão de Autenticação do SQL Server e é membro da função de servidor fixa **sysadmin**. Para obter mais informações sobre Modos de Autenticação, veja [Choose an Authentication Mode](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) (Escolher um Modo de Autenticação).

Se não ativar a Autenticação do SQL Server, em seguida, pode utilizar a conta de Administrador local na VM para ligar à instância do SQL Server.

### <a name="storage-configuration"></a>Configuração do armazenamento

Clique em **Configuração do armazenamento** para especificar os requisitos de armazenamento.

![Configuração do Armazenamento do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

> [!NOTE]
> Se tiver configurado manualmente a VM para utilizar o armazenamento standard, esta opção não estará disponível. A otimização de armazenamento automática só está disponível para o Premium Storage.

> [!TIP]
> O número de paragens e os limites superiores de cada controlo de deslize dependem do tamanho da VM que selecionou. Quanto maiores e mais poderosas, mais aumentadas verticalmente podem ser as VMs.

Pode especificar os requisitos como operações de entrada/saída por segundo (IOPs), débito em MB/s e tamanho de armazenamento total. Configure estes valores, utilizando as escalas deslizantes. Pode alterar estas definições de armazenamento com base na carga de trabalho. O portal calcula automaticamente o número de discos a ligar e configurar com base nestes requisitos.

Em **Armazenamento otimizado para**, selecione uma das seguintes opções:

* **Geral** é a predefinição e suporta a maioria das cargas de trabalho.
* O processamento **Transacional** otimiza o armazenamento para cargas de trabalho OLTP de bases de dados tradicionais.
* O **Armazenamento de dados** otimiza o armazenamento para cargas de trabalho analíticas e de relatórios.

### <a name="automated-patching"></a>Aplicação de patches automatizada

A **Aplicação de patches automatizada** está ativada por predefinição. A Aplicação de patches automatizada permite ao Azure automaticamente aplicar o patch do SQL Server e o sistema operativo. Especifique um dia da semana, a hora e a duração de uma janela de manutenção. O Azure executa a aplicação de patches nesta janela de manutenção. A agenda da janela de manutenção utiliza a região da VM para a hora. Se não pretender que o Azure aplique automaticamente o patch do SQL Server e o sistema operativo, clique em **Desativar**.  

![Aplicação de Patches Automatizada do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Para obter mais informações, consulte o artigo [Aplicação de Patches Automatizada para o SQL Server nas Virtual Machines do Azure](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Cópia de segurança automatizada

Ative as cópias de segurança de bases de dados automáticas em **Cópia de segurança automatizada**. A cópia de segurança automatizada está desativada por predefinição.

Quando ativa a cópia de segurança automatizada do SQL, pode configurar as seguintes definições:

* Período de retenção (dias) para cópias de segurança
* Conta do Storage a utilizar para as cópias de segurança
* Opção de encriptação e palavra-passe para as cópias de segurança
* Realizar cópia de segurança das bases de dados do sistema
* Configurar agenda da cópia de segurança

Para encriptar a cópia de segurança, clique em **Ativar**. Em seguida, especifique a **Palavra-passe**. O Azure cria um certificado para encriptar as cópias de segurança e utiliza a palavra-passe especificada para proteger o certificado.

![Cópia de Segurança Automatizada do SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup2.png)

 Para obter mais informações, consulte o artigo [Cópia de Segurança Automatizada para o SQL Server nas Virtual Machines do Azure](virtual-machines-windows-sql-automated-backup.md).

### <a name="azure-key-vault-integration"></a>Integração do Cofre de Chaves do Azure

Para armazenar segredos de segurança no Azure para a encriptação, clique em **Integração do cofre de chaves do Azure** e clique em **Ativar**.

![Integração do Cofre de Chaves do SQL Azure](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

A tabela seguinte lista os parâmetros necessários para configurar a Integração do Cofre de Chaves do Azure.

| PARÂMETRO | DESCRIÇÃO | EXEMPLO |
| --- | --- | --- |
| **URL do Cofre de Chaves** |A localização do cofre de chaves. |<https://contosokeyvault.vault.azure.net/> |
| **Nome principal** |Nome principal do serviço Azure Active Directory. Este nome também é referido como o ID de Cliente. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Segredo principal** |Segredo principal do serviço Azure Active Directory. Este segredo também é referido como o Segredo do Cliente. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Nome da credencial** |**Nome da credencial**: A integração AKV cria uma credencial dentro do SQL Server, permitindo que a VM tem acesso ao Cofre de chaves. Escolha um nome para esta credencial. |mycred1 |

Para obter mais informações, consulte o artigo [Configurar a Integração do Cofre de Chaves do Azure para o SQL Server em VMs do Azure](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="sql-server-machine-learning-services"></a>Serviços de Machine Learning do SQL Server

Tem a opção de ativar os [Serviços de Machine Learning do SQL Server](https://msdn.microsoft.com/library/mt604845.aspx). Esta opção permite-lhe utilizar a análise avançada com o SQL Server 2017. Clique em **Ativar** na janela **Definições do SQL Server**.

![Ativar Serviços de Machine Learning do SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

Quando tiver terminado de configurar as definições do SQL Server, clique em **OK**.

## <a name="5-review-the-summary"></a>5. Reveja o resumo

Na janela **Resumo**, reveja o resumo e clique em **Comprar** para criar o SQL Server, o grupo de recursos e os recursos especificados para esta VM.

Pode monitorizar a implementação a partir do portal do Azure. O botão **Notificações** na parte superior do ecrã mostra o estado básico da implementação.

> [!NOTE]
> Para lhe fornecer uma ideia dos tempos da implementação, implementei uma VM do SQL para a região EUA Leste com as predefinições. Esta implementação de teste demorou aproximadamente 12 minutos a concluir. Mas poderá ter uma implementação mais lenta ou mais rápida com base na sua região e nas definições selecionadas.

## <a id="remotedesktop"></a> Abrir a VM com o Ambiente de Trabalho Remoto

Utilize os seguintes passos para ligar à máquina virtual do SQL Server com o Ambiente de Trabalho Remoto:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Depois de ligar à máquina virtual do SQL Server, pode iniciar o SQL Server Management Studio e estabelecer ligação à Autenticação do Windows com as suas credenciais de administrador local. Se ativou a Autenticação do SQL Server, também pode ligar com Autenticação do SQL Server com o início de sessão do SQL e a palavra-passe que configurou durante o aprovisionamento.

O acesso ao computador permite-lhe alterar diretamente as definições do SQL Server e da máquina com base nos seus requisitos. Por exemplo, pode configurar as definições da firewall ou alterar as definições de configuração do SQL Server.

## <a id="connect"></a> Ligar ao SQL Server remotamente

Nestas instruções, que selecionou **pública** acesso para a máquina virtual e **autenticação do SQL Server**. Estas definições configuraram automaticamente a máquina virtual para permitir ligações ao SQL Server a partir de qualquer cliente através da Internet (partindo do princípio de que tem o início de sessão SQL correto).

> [!NOTE]
> Se não selecionou Público durante o aprovisionamento, pode alterar as definições de conectividade SQL através do portal após o aprovisionamento. Para obter mais informações, veja o artigo [Alterar as definições de conectividade SQL](virtual-machines-windows-sql-connect.md#change).

As secções seguintes mostram como ligar através da internet para a sua instância de VM do SQL Server.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Próximos Passos

Para outras informações sobre como utilizar o SQL Server no Azure, consulte o artigo [SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-server-iaas-overview.md) e as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).