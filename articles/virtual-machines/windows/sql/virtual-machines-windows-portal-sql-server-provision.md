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
ms.openlocfilehash: bb051d37f3a1dd82d7d46bfe8b22c2ba1251be85
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59259206"
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

1. No campo de pesquisa, escreva **SQL Server 2017** e prima ENTER.

1. No filtro listas pendentes, selecione _Windows Server 2016_ para o **sistema operativo** e selecione _Microsoft_ como o **publicador**. 

     ![Janela Nova Computação](./media/virtual-machines-windows-portal-sql-server-provision/azure-new-compute-blade.png)

1. Reveja as imagens do SQL Server disponíveis. Cada imagem identifica uma versão do SQL Server e um sistema operativo.

1. Selecione a imagem com o nome **licença gratuita do SQL Server: O SQL Server 2017 Developer no Windows Server 2016**.

   > [!TIP]
   > A edição de programador é utilizada nestas instruções porque é uma edição completa, gratuita do SQL Server para teste de desenvolvimento. Apenas paga pelo custo de execução da VM. No entanto, é livre para escolher qualquer uma das imagens para utilizar esta explicação passo a passo. Para obter uma descrição de imagens disponíveis, consulte a [descrição geral de máquinas virtuais do SQL Server Windows](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

   > [!TIP]
   > Custos de licenciamento para o SQL Server estão incorporados nos preços da VM, criar e varia por segundo, edição e núcleos. No entanto, edição do SQL Server Developer é gratuita para programação/teste (não produção) e o SQL Express é gratuito para cargas de trabalho leves (inferiores a 1 GB de memória inferior a 10 GB de armazenamento). Também pode trazer-sua própria licença (BYOL) e pagar apenas para a VM. Aos nomes dessas imagem é adicionado o prefixo {BYOL}. 
   >
   > Para obter mais informações sobre estas opções, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).

1. Em **Selecionar um modelo de implementação**, confirme se **Resource Manager** está selecionado. O modelo de implementação recomendado para máquinas virtuais novas é o Resource Manager. 

1. Selecione **Criar**.


## <a id="configure"></a> Opções de configuração

Existem vários separadores para configurar uma máquina virtual do SQL Server. Para efeitos deste manual, nos concentraremos no seguinte: 

| Passo | Descrição |
| --- | --- |
| **Noções básicas** |[Configurar as definições básicas](#1-configure-basic-settings) |
| **Funcionalidades opcionais** |[Configurar funcionalidades opcionais](#2-configure-optional-features) |
| **Definições do SQL Server** |[Configurar definições do SQL Server](#3-configure-sql-server-settings) |
| **Rever + criar** | [Reveja o resumo](#4-review--create) |

## <a name="1-configure-basic-settings"></a>1. Configurar as definições básicas


Sobre o **Noções básicas** separador, forneça as seguintes informações:

* Sob **detalhes do projeto**, certifique-se de que a subscrição correta está selecionada. 
*  Na **grupo de recursos** secção, selecione um recurso existente de grupo da lista ou escolha **criar nova** para criar um novo grupo de recursos. Um grupo de recursos é uma coleção de recursos relacionados no Azure (máquinas virtuais, contas do Storage, redes virtuais, etc.). 

    ![Subscrição](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > Utilizar um novo grupo de recursos é útil se estiver apenas a testar ou a saber mais sobre implementações do SQL Server no Azure. Depois de terminar o teste, elimine o grupo de recursos para eliminar automaticamente a VM e todos os recursos associados a esse grupo de recursos. Para mais informações sobre grupos de recursos, consulte o artigo [Descrição Geral do Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md).


* Sob **detalhes de instância**:
    1. Introduza uma única **nome da Máquina Virtual**.  
    1. Escolha uma localização para o seu **região**. 
    1. Para efeitos deste manual, deixe **opções de disponibilidade** definida como _nenhuma redundância de infraestrutura necessária_. Para obter mais informações sobre as opções de disponibilidade, veja [regiões do Azure e a disponibilidade](../../windows/regions-and-availability.md). 
    1. Na **imagem** lista, selecione _licença gratuita do SQL Server: O SQL Server 2017 Developer no Windows Server 2016_.  
    1. Optar por **alterar o tamanho** para o **tamanho** da máquina virtual e selecione o **A2 básica** oferta. Certifique-se de que limpe os recursos, uma vez que terminar de usá-los para evitar quaisquer cobranças inesperadas. Relativamente a cargas de trabalho de produção, veja os tamanhos e as configurações de máquinas recomendados em [Performance best practices for SQL Server in Azure Virtual Machine](virtual-machines-windows-sql-performance.md) (Melhores práticas de desempenho para o SQL Server nas Máquinas Virtuais do Azure).

    ![Detalhes da instância](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> O custo mensal estimado apresentado na janela **Escolher um tamanho** não inclui os custos de licenciamento do SQL Server. Esta estimativa é o custo da VM autónoma. Para as edições Express e Developer do SQL Server, esta estimativa é o custo total estimado. Quanto a outras edições, veja a [página de preços das Máquinas Virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e selecione a edição de destino do SQL Server. Consulte também os [preços orientações para VMs do Azure do SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md) e [tamanhos de máquinas virtuais](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Sob **conta de administrador**, forneça um nome de utilizador e uma palavra-passe. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Conta de administrador](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* Sob **regras de porta de entrada**, escolha **permitir portas selecionadas** e, em seguida, selecione **RDP (. 3389)** na lista suspensa. 

   ![Regras da porta de entrada](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Configurar funcionalidades opcionais

### <a name="disks"></a>Discos

Sobre o **discos** separador, configure as opções de disco. 

* Sob **tipo de disco de SO**, selecione o tipo de disco que pretende para o sistema operacional da lista pendente. Premium é recomendado para sistemas de produção, mas não está disponível para uma VM básica. Para utilizar o Premium SSD, altere o tamanho de máquina virtual. 
* Sob **avançadas**, selecione **Sim** em utilização **Managed Disks**.

   > [!NOTE]
   > A Microsoft recomenda o Managed Disks para o SQL Server. O Managed Disks processa o armazenamento em segundo plano. Além disso, se houver máquinas virtuais com Managed Disks no mesmo conjunto de disponibilidade, o Azure distribui os recursos de armazenamento para fornecer a redundância adequada. Para obter mais informações, consulte [Managed Disks descrição geral do Azure] [... / geridos-discos-overview.md). Para obter informações específicas sobre os discos geridos num conjunto de disponibilidade, veja [utilizar discos geridos para VMs no conjunto de disponibilidade] (... /manage-Availability.md.

![Definições do disco de VM do SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Redes

Sobre o **redes** separador, configure as opções de funcionamento em rede. 

* Criar uma nova **rede virtual**, ou utilizar uma vNet já existente para a sua VM do SQL Server. Designar uma **sub-rede** também. 

* Sob **grupo de segurança NIC**, selecione um grupo de segurança básica ou o grupo de segurança avançada. Escolher a opção de básica poder selecionar portas de entrada para VM do SQL Server (os mesmos valores que foram configurados no **básica** separador). Selecionar a opção avançada permite-lhe escolher um grupo de segurança de rede existente ou crie um novo. 

* Pode fazer outras alterações às definições de rede, ou mantenha os valores predefinidos.

![Configurações de rede de VM do SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitorização

Sobre o **monitorização** separador, configurar a monitorização e de encerramento automático. 

* Do Azure permite **de monitoramento de inicialização** por padrão com a mesma conta de armazenamento designada para a VM. Pode alterar estas definições aqui, bem como a permissão **SO convidado diagnóstico**. 
* Pode habilitar **sistema atribuído a identidade gerida** e **encerramento automático** neste separador também. 

![Definições de gestão da VM do SQL Server](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Configurar definições do SQL Server

Sobre o **definições do SQL Server** separador, configure as definições específicas e otimizações para o SQL Server. As definições que pode configurar para o SQL Server incluem o seguinte:



| Definição |
| --- |
| [Conetividade](#connectivity) |
| [Autenticação](#authentication) |
| [Integração do Cofre de Chaves do Azure](#azure-key-vault-integration) |
| [Configuração do armazenamento](#storage-configuration) |
| [Aplicação de Patches Automatizada](#automated-patching) |
| [Cópia de Segurança Automatizada](#automated-backup) |
| [Serviços R (Advanced Analytics)](#r-services-advanced-analytics) |


### <a name="connectivity"></a>Conectividade

Em **Conectividade do SQL**, especifique o tipo de acesso que pretende para a instância do SQL Server nesta VM. Para efeitos nestas instruções, selecione **público (internet)** para permitir ligações ao SQL Server a partir de máquinas ou serviços na internet. Com esta opção selecionada, o Azure configura automaticamente a firewall e o grupo de segurança de rede para permitir o tráfego na porta selecionada.

> [!TIP]
> Por predefinição, o SQL Server escuta numa porta bem conhecida, a **1433**. Para maior segurança, altere a porta na caixa de diálogo anterior para escutar numa porta não predefinida, como 1401. Se alterar a porta, tem de ligar com essa porta qualquer das ferramentas de cliente, como o SSMS.

![Segurança de VM do SQL Server](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

Para ligar ao SQL Server através da Internet, também tem de ativar a Autenticação do SQL Server, que se descreve na secção seguinte.

Se preferir não ativar ligações para o Motor de Base de dados através da Internet, escolha uma das seguintes opções:

* **Local (no interior da VM)** para permitir ligações ao SQL Server, apenas a partir de dentro da VM.
* **Privada (na Virtual Network)** para permitir ligações ao SQL Server a partir de máquinas ou serviços na mesma rede virtual.

Em geral, melhore a segurança, selecionando a conectividade mais restritiva que permite o seu cenário. Mas todas as opções têm capacidade de segurança através das regras do Grupo de Segurança de Rede e Autenticação do SQL/Windows. Pode editar o Grupo de Segurança de Rede após a VM ter sido criada. Para obter mais informações, veja [Security Considerations for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-security.md) (Considerações de segurança para o SQL Server em Máquinas Virtuais do Azure).



### <a name="authentication"></a>Authentication

Se necessitar da Autenticação do SQL Server, clique em **Ativar** em **Autenticação do SQL**.

![Autenticação do SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> Se pretender aceder ao SQL Server através da internet (a opção de conectividade pública), tem de ativar autenticação do SQL aqui. O acesso público ao SQL Server requer a utilização da Autenticação do SQL.

Se ativar a Autenticação do SQL Server, especifique um **Nome de início de sessão** e uma **Palavra-passe**. Este nome de início de sessão é configurado como um início de sessão de autenticação do SQL Server e o membro do **sysadmin** função de servidor fixa. Para obter mais informações sobre Modos de Autenticação, veja [Choose an Authentication Mode](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) (Escolher um Modo de Autenticação).

Se não ativar a Autenticação do SQL Server, em seguida, pode utilizar a conta de Administrador local na VM para ligar à instância do SQL Server.

![Autenticação do SQL Server](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

### <a name="azure-key-vault-integration"></a>Integração do Cofre de Chaves do Azure

Para armazenar segredos de segurança no Azure para a encriptação, clique em **Integração do cofre de chaves do Azure** e clique em **Ativar**.

![Integração do Cofre de Chaves do Azure](media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

A tabela seguinte lista os parâmetros necessários para configurar a Integração do Cofre de Chaves do Azure.

| PARÂMETRO | DESCRIÇÃO | EXEMPLO |
| --- | --- | --- |
| **URL do Cofre de Chaves** |A localização do cofre de chaves. |https:\//contosokeyvault.vault.azure.net/ |
| **Nome principal** |Nome principal do serviço Azure Active Directory. Este nome também é referido como o ID de Cliente. |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **Segredo principal** |Segredo principal do serviço Azure Active Directory. Este segredo também é referido como o Segredo do Cliente. |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **Nome da credencial** |**Nome da credencial**: A integração AKV cria uma credencial dentro do SQL Server, permitindo que a VM tem acesso ao Cofre de chaves. Escolha um nome para esta credencial. |mycred1 |

Para obter mais informações, consulte o artigo [Configurar a Integração do Cofre de Chaves do Azure para o SQL Server em VMs do Azure](virtual-machines-windows-ps-sql-keyvault.md).

### <a name="storage-configuration"></a>Configuração do armazenamento

Sob **configuração de armazenamento**, selecione **alterar a configuração** para especificar os requisitos de armazenamento.


> [!NOTE]
> Se tiver configurado manualmente a VM para utilizar o armazenamento standard, esta opção não estará disponível. A otimização de armazenamento automática só está disponível para o Premium Storage.

> [!TIP]
> O número de paragens e os limites superiores de cada controlo de deslize dependem do tamanho da VM que selecionou. Quanto maiores e mais poderosas, mais aumentadas verticalmente podem ser as VMs.

Pode especificar os requisitos como operações de entrada/saída por segundo (IOPs), débito em MB/s e tamanho de armazenamento total. Configure estes valores, utilizando as escalas deslizantes. Pode alterar estas definições de armazenamento com base na carga de trabalho. O portal calcula automaticamente o número de discos a ligar e configurar com base nestes requisitos.

Em **Armazenamento otimizado para**, selecione uma das seguintes opções:

* **Geral** é a predefinição e suporta a maioria das cargas de trabalho.
* O processamento **Transacional** otimiza o armazenamento para cargas de trabalho OLTP de bases de dados tradicionais.
* O **Armazenamento de dados** otimiza o armazenamento para cargas de trabalho analíticas e de relatórios.

![Configuração de armazenamento de VM do SQL](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-storage-configuration.png)

### <a name="sql-server-license"></a>Licença do SQL Server
Se for um cliente de Software Assurance, pode utilizar o [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para trazer a sua licença do SQL Server e poupar recursos. 

![Licença de VM do SQL Server](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Aplicação de patches automatizada

A **Aplicação de patches automatizada** está ativada por predefinição. A Aplicação de patches automatizada permite ao Azure automaticamente aplicar o patch do SQL Server e o sistema operativo. Especifique um dia da semana, a hora e a duração de uma janela de manutenção. O Azure executa a aplicação de patches nesta janela de manutenção. A agenda da janela de manutenção utiliza a região da VM para a hora. Se não pretender que o Azure aplique automaticamente o patch do SQL Server e o sistema operativo, clique em **Desativar**.  

![Aplicação de patches automatizada de VM do SQL Server](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

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

Para obter mais informações, consulte o artigo [Cópia de Segurança Automatizada para o SQL Server nas Virtual Machines do Azure](virtual-machines-windows-sql-automated-backup.md).


### <a name="r-services-advanced-analytics"></a>Serviços R (Advanced Analytics)

Tem a opção para ativar [SQL Server R Services (análise avançada)](/sql/advanced-analytics/r/sql-server-r-services/). Esta opção permite-lhe utilizar a análise avançada com o SQL Server 2017. Clique em **Ativar** na janela **Definições do SQL Server**.


## <a name="4-review--create"></a>4. Rever + criar

Sobre o **rever + criar** separador, reveja o resumo e selecione **criar** para criar o SQL Server, o grupo de recursos e recursos especificados para esta VM.

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

  > [!NOTE]
  > Este exemplo utiliza a porta 1433 comum. No entanto, este valor precisará ser alterada caso uma porta diferente (como 1401) foi especificada durante a implementação de VM do SQL Server. 


## <a name="next-steps"></a>Passos Seguintes

Para outras informações sobre como utilizar o SQL Server no Azure, consulte o artigo [SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-server-iaas-overview.md) e as [Perguntas Mais Frequentes](virtual-machines-windows-sql-server-iaas-faq.md).