---
title: Fornecimento de uma máquina virtual Windows com o portal Azure
description: Este guia cobre as opções disponíveis para a utilização do portal Azure para o fornecimento do SQL Server numa máquina virtual Windows.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: f0b7e04becd42180df3393d7036294c1629cdc21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499497"
---
# <a name="how-to-use-the-azure-portal-to-provision-a-windows-virtual-machine-with-sql-server"></a>Como utilizar o portal Azure para a provisionar uma máquina virtual Windows com o SQL Server

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este guia cobre as opções disponíveis para a utilização do portal Azure para o fornecimento do SQL Server numa máquina virtual Windows (VM). Este artigo cobre mais opções de configuração do que o [quickstart SQL Server VM](sql-vm-create-portal-quickstart.md), que se foca mais profundamente numa única configuração. 

Utilize este guia para criar o seu próprio SQL Server VM. Ou, use-o como referência para as opções disponíveis no portal Azure.

> [!TIP]
> Se tiver dúvidas sobre máquinas virtuais do SQL Server, veja as [Perguntas Mais Frequentes](frequently-asked-questions-faq.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a> Imagens de galeria de máquinas virtuais do SQL Server

Quando criar uma máquina virtual SQL Server, pode selecionar uma de várias imagens pré-configuradas da galeria de máquinas virtuais. Os seguintes passos demonstram como selecionar uma das imagens do SQL Server 2017.

1. Selecione **Azure SQL** no menu esquerdo do portal Azure. Se **o Azure SQL** não estiver na lista, selecione **Todos os serviços,** escreva *Azure SQL* na caixa de pesquisa. 

   Também pode selecionar a estrela ao lado do **Azure SQL** para guardá-la como favorita e adicioná-la como um item na navegação à esquerda. 

1. **Selecione + Adicione** para abrir a página de **opção de implementação Select SQL.** Pode ver informações adicionais selecionando **detalhes do Show**. 
1. Digite *2017* na caixa de pesquisa de imagem do SQL Server no azulejo **das máquinas virtuais SQL** e, em seguida, selecione **Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016** from the drop-down. 

   ![Selecione a imagem VM SQL](./media/create-sql-vm-portal/select-sql-vm-image-portal.png)

   > [!TIP]
   > A edição developer é usada neste artigo porque é uma edição gratuita e completa do SQL Server para testes de desenvolvimento. Apenas paga pelo custo de execução da VM. No entanto, você é livre de escolher qualquer uma das imagens para usar neste walkthrough. Para obter uma descrição das imagens disponíveis, consulte a visão geral das [máquinas virtuais do SQL Server Windows](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

   > [!TIP]
   > Os custos de licenciamento para o SQL Server são incorporados no preço por segundo do VM que cria e varia por edição e núcleos. No entanto, a edição do SQL Server Developer é gratuita para desenvolvimento e testes, não para produção. Além disso, o SQL Express é gratuito para cargas de trabalho leves (menos de 1 GB de memória, menos de 10 GB de armazenamento). Também pode trazer a sua própria licença (BYOL) e pagar apenas pelo VM. Aos nomes dessas imagem é adicionado o prefixo {BYOL}. 
   >
   > Para obter mais informações sobre estas opções, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](pricing-guidance.md).


1. Selecione **Criar**.


## <a name="1-configure-basic-settings"></a>1. Configurar as definições básicas

No **separador Básicos,** forneça as seguintes informações:

* Em **Detalhes do Projeto,** certifique-se de que a subscrição correta está selecionada. 
* Na secção **grupo de Recursos,** selecione um grupo de recursos existente da lista ou escolha **Criar novo** para criar um novo grupo de recursos. Um grupo de recursos é uma coleção de recursos relacionados no Azure (máquinas virtuais, contas do Storage, redes virtuais, etc.). 

  ![Subscrição](./media/create-sql-vm-portal/basics-project-details.png)

  > [!NOTE]
  > Utilizar um novo grupo de recursos é útil se estiver apenas a testar ou a saber mais sobre implementações do SQL Server no Azure. Depois de terminar o teste, elimine o grupo de recursos para eliminar automaticamente a VM e todos os recursos associados a esse grupo de recursos. Para obter mais informações sobre grupos de recursos, consulte [a visão geral do Gestor de Recursos Azure](../../../active-directory-b2c/overview.md).


* De **acordo com os detalhes do exemplo:**

    1. Introduza um nome de **máquina virtual** único.  
    1. Escolha um local para a sua **Região.** 
    1. Para efeitos deste guia, deixe **as opções de disponibilidade definidas** sem _necessidade de redundância de infraestrutura._ Para obter mais informações sobre as opções de disponibilidade, consulte [Disponibilidade.](../../../virtual-machines/availability.md) 
    1. Na lista **de imagens,** selecione _Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016_.  
    1. Opte por alterar o **tamanho** do **tamanho** da máquina virtual e selecione a oferta **A2 Basic.** Certifique-se de limpar os seus recursos assim que terminar com eles para evitar quaisquer acusações inesperadas. Relativamente a cargas de trabalho de produção, veja os tamanhos e as configurações de máquinas recomendados em [Performance best practices for SQL Server in Azure Virtual Machine](performance-guidelines-best-practices.md) (Melhores práticas de desempenho para o SQL Server nas Máquinas Virtuais do Azure).

    ![Detalhes da instância](./media/create-sql-vm-portal/basics-instance-details.png)

> [!IMPORTANT]
> O custo mensal estimado apresentado na janela **Escolher um tamanho** não inclui os custos de licenciamento do SQL Server. Esta estimativa é apenas o custo do VM. Para as edições Express e Developer do SQL Server, esta estimativa é o custo total estimado. Quanto a outras edições, veja a [página de preços das Máquinas Virtuais do Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e selecione a edição de destino do SQL Server. Consulte também [a orientação de preços para SQL Server Azure VMs](pricing-guidance.md) e [Tamanhos para máquinas virtuais](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Na **conta do Administrador,** forneça um nome de utilizador e uma palavra-passe. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Conta de administrador](./media/create-sql-vm-portal/basics-administrator-account.png)

* De acordo com **as regras da porta de entrada**, escolha Localizar portas **selecionadas** e, em seguida, selecionar **RDP (3389)** a partir do drop-down. 

   ![Regras da porta de entrada](./media/create-sql-vm-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. Configurar características opcionais

### <a name="disks"></a>Discos

No **separador Discos,** configuure as opções do disco. 

* Sob **o tipo de disco SO,** selecione o tipo de disco que deseja para o seu SO a partir do drop-down. O prémio é recomendado para os sistemas de produção, mas não está disponível para um VM básico. Para utilizar um SSD Premium, mude o tamanho da máquina virtual. 
* Em **Avançado**, selecione **Sim** sob a utilização de **Discos Geridos**.

   > [!NOTE]
   > A Microsoft recomenda o Managed Disks para o SQL Server. O Managed Disks processa o armazenamento em segundo plano. Além disso, se houver máquinas virtuais com Managed Disks no mesmo conjunto de disponibilidade, o Azure distribui os recursos de armazenamento para fornecer a redundância adequada. Para mais informações, consulte [a visão geral dos discos geridos aZure](../../../virtual-machines/managed-disks-overview.md). Para obter informações específicas sobre os discos geridos em conjuntos de disponibilidade, veja [Use managed disks for VMs in availability set](../../../virtual-machines/availability.md) (Utilizar discos geridos em VMs num conjunto de disponibilidade).

![Definições de disco VM SQL](./media/create-sql-vm-portal/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Rede

No **separador Networking,** configuure as suas opções de networking. 

* Crie uma nova **rede virtual** ou utilize uma rede virtual existente para o seu SQL Server VM. Designe um **Subnet** também. 

* No âmbito **do grupo de segurança da rede NIC,** selecione um grupo de segurança básico ou o grupo de segurança avançado. A escolha da opção básica permite-lhe selecionar portas de entrada para o SQL Server VM que são os mesmos valores configurados no separador **Basic.** Selecionar a opção avançada permite-lhe escolher um grupo de segurança de rede existente ou criar um novo. 

* Pode es fazer outras alterações nas definições de rede ou manter os valores predefinidos.

![Definições de rede SQL VM](./media/create-sql-vm-portal/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Monitorização

No **separador Monitor,** configurar a monitorização e desligar automaticamente. 

* O Azure permite **diagnósticos de boot** por padrão com a mesma conta de armazenamento designada para o VM. Neste separador, pode alterar estas definições e ativar **os diagnósticos dos hóspedes do SO**. 
* Também pode ativar **a identidade gerida e** o encerramento automático **do** Sistema neste separador. 

![Definições de gestão de VM SQL](./media/create-sql-vm-portal/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Configurar as definições do Servidor SQL

No separador de definições do **SQL Server,** configurar definições específicas e otimizações para o SQL Server. Pode configurar as seguintes definições para O Servidor SQL:

- [Conetividade](#connectivity)
- [Autenticação](#authentication)
- [Integração do Cofre chave Azure](#azure-key-vault-integration)
- [Configuração do armazenamento](#storage-configuration)
- [Remendação automatizada](#automated-patching)
- [Backup automatizado](#automated-backup)
- [Machine Learning Services](#machine-learning-services)


### <a name="connectivity"></a>Conectividade

Em **Conectividade do SQL**, especifique o tipo de acesso que pretende para a instância do SQL Server nesta VM. Para efeitos desta passagem, selecione **Public (internet)** para permitir ligações ao SQL Server a partir de máquinas ou serviços na internet. Com esta opção selecionada, o Azure configura automaticamente a firewall e o grupo de segurança da rede para permitir o tráfego na porta selecionada.

> [!TIP]
> Por predefinição, o SQL Server escuta numa porta bem conhecida, a **1433**. Para maior segurança, altere a porta na caixa de diálogo anterior para escutar numa porta não predefinida, como 1401. Se alterar a porta, deve ligar-se utilizando essa porta a partir de quaisquer ferramentas de cliente, como o SQL Server Management Studio (SSMS).

![Segurança SQL VM](./media/create-sql-vm-portal/azure-sqlvm-security.png)

Para ligar ao SQL Server através da Internet, também tem de ativar a Autenticação do SQL Server, que se descreve na secção seguinte.

Se preferir não ativar ligações para o Motor de Base de dados através da Internet, escolha uma das seguintes opções:

* **Local (no interior da VM)** para permitir ligações ao SQL Server, apenas a partir de dentro da VM.
* **Privada (na Virtual Network)** para permitir ligações ao SQL Server a partir de máquinas ou serviços na mesma rede virtual.

Em geral, melhore a segurança, selecionando a conectividade mais restritiva que permite o seu cenário. Mas todas as opções são possíveis através das regras do grupo de segurança de rede (NSG) e da Autenticação SQL/Windows. Pode editar o NSG após a criação do VM. Para obter mais informações, veja [Security Considerations for SQL Server in Azure Virtual Machines](security-considerations-best-practices.md) (Considerações de segurança para o SQL Server em Máquinas Virtuais do Azure).

### <a name="authentication"></a>Autenticação

Se necessitar de autenticação do servidor SQL, **selecione Ative** under **SQL Authentication** no separador **de definições do SQL Server.**

![Autenticação do SQL Server](./media/create-sql-vm-portal/azure-sqlvm-authentication.png)

> [!NOTE]
> Se planeia aceder ao SQL Server através da internet (a opção de conectividade pública), tem de ativar a autenticação SQL aqui. O acesso público ao SqL Server requer autenticação SQL.

Se ativar a Autenticação do SQL Server, especifique um **Nome de início de sessão** e uma **Palavra-passe**. Este nome de login é configurado como um login de autenticação do servidor SQL e um membro da função de servidor fixo **sysadmin.** Para obter mais informações sobre Modos de Autenticação, veja [Choose an Authentication Mode](/sql/relational-databases/security/choose-an-authentication-mode) (Escolher um Modo de Autenticação).

Se preferir não ativar a autenticação do servidor SQL, pode utilizar a conta de Administrador local na VM para ligar à instância do SQL Server.

### <a name="azure-key-vault-integration"></a>Integração do Cofre de Chaves do Azure

Para armazenar segredos de segurança no Azure para encriptação, selecione **as definições do SQL Server** e desloque-se até à  **integração do cofre de chaves Azure**. **Selecione Ative** e preencha as informações solicitadas. 

![Integração do Cofre de Chaves do Azure](./media/create-sql-vm-portal/azure-sqlvm-akv.png)

A tabela que se segue lista os parâmetros necessários para configurar a integração do Cofre da Chave Azure (AKV).

| PARÂMETRO | DESCRIÇÃO | EXEMPLO |
| --- | --- | --- |
| **URL do Cofre de Chaves** |A localização do cofre de chaves. |`https://contosokeyvault.vault.azure.net/` |
| **Nome principal** |Nome principal do serviço Azure Active Directory. Este nome também é referido como o ID de Cliente. |`fde2b411-33d5-4e11-af04eb07b669ccf2` |
| **Segredo principal** |Segredo principal do serviço Azure Active Directory. Este segredo também é referido como o Segredo do Cliente. |`9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=` |
| **Nome da credencial** |**Nome credencial**: A AKV Integration cria uma credencial dentro do SQL Server e permite que o VM aceda ao cofre da chave. Escolha um nome para esta credencial. |`mycred1` |

Para obter mais informações, consulte o artigo [Configurar a Integração do Cofre de Chaves do Azure para o SQL Server em VMs do Azure](azure-key-vault-integration-configure.md).

### <a name="storage-configuration"></a>Configuração do armazenamento

No separador de definições do **SERVIDOR SQL,** na **configuração de Armazenamento**, selecione alterar a **configuração** para abrir a página de Configuração de Armazenamento Otimizada de Desempenho e especificar os requisitos de armazenamento.

![Screenshot que realça onde pode alterar a configuração de armazenamento.](./media/create-sql-vm-portal/sql-vm-storage-configuration-provisioning.png)

Em **Armazenamento otimizado para**, selecione uma das seguintes opções:

* **Geral** é a predefinição e suporta a maioria das cargas de trabalho.
* **O processamento transacional** otimiza o armazenamento para as cargas de trabalho tradicionais da base de dados OLTP.
* O **Armazenamento de dados** otimiza o armazenamento para cargas de trabalho analíticas e de relatórios.

![Configuração de armazenamento DE VM SQL](./media/create-sql-vm-portal/sql-vm-storage-configuration.png)

Pode optar por deixar os valores em predefinição ou alterar manualmente a topologia de armazenamento de acordo com as suas necessidades de IOPS. Para obter mais informações, consulte [a configuração de armazenamento.](storage-configuration.md) 

### <a name="sql-server-license"></a>Licença do Servidor SQL

Se for cliente de Software Assurance, pode utilizar o [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) para trazer a sua própria licença SQL Server e economizar em recursos. 

![Licença SQL VM](./media/create-sql-vm-portal/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Aplicação de patches automatizada

A **Aplicação de patches automatizada** está ativada por predefinição. A Aplicação de patches automatizada permite ao Azure automaticamente aplicar o patch do SQL Server e o sistema operativo. Especifique um dia da semana, a hora e a duração de uma janela de manutenção. O Azure executa a aplicação de patches nesta janela de manutenção. O horário da janela de manutenção utiliza o local VM. Se não quiser que o Azure remete automaticamente o SQL Server e o sistema operativo, selecione **Desativar**.  

![Patching automatizado SQL VM](./media/create-sql-vm-portal/azure-sqlvm-automated-patching.png)

Para obter mais informações, consulte o artigo [Aplicação de Patches Automatizada para o SQL Server nas Virtual Machines do Azure](automated-patching.md).

### <a name="automated-backup"></a>Cópia de segurança automatizada

Ative as cópias de segurança de bases de dados automáticas em **Cópia de segurança automatizada**. A cópia de segurança automatizada está desativada por predefinição.

Quando ativa a cópia de segurança automatizada do SQL, pode configurar as seguintes definições:

* Período de retenção (dias) para cópias de segurança
* Conta do Storage a utilizar para as cópias de segurança
* Opção de encriptação e palavra-passe para as cópias de segurança
* Realizar cópia de segurança das bases de dados do sistema
* Configurar agenda da cópia de segurança

Para encriptar a cópia de segurança, selecione **Ative**. Em seguida, especifique a **Palavra-passe**. O Azure cria um certificado para encriptar as cópias de segurança e utiliza a palavra-passe especificada para proteger o certificado. Por predefinição, o horário é definido automaticamente, mas pode criar um horário manual selecionando **Manual**. 

![Backups automatizados SQL VM](./media/create-sql-vm-portal/automated-backup.png)

Para obter mais informações, consulte o artigo [Cópia de Segurança Automatizada para o SQL Server nas Virtual Machines do Azure](automated-backup-sql-2014.md).


### <a name="machine-learning-services"></a>Machine Learning Services

Tem a opção de ativar [os Serviços de Aprendizagem automática.](/sql/advanced-analytics/) Esta opção permite-lhe utilizar machine learning com Python e R no SQL Server 2017. **Selecione Ative** na janela **definições do servidor SQL.**


## <a name="4-review--create"></a>4. Revisão + criar

No **separador 'Rever +' criar:**
1. Reveja o resumo.
1. Selecione **Criar** para criar o SQL Server, grupo de recursos e recursos especificados para este VM.

Pode monitorizar a implementação a partir do portal do Azure. O botão **Notificações** na parte superior do ecrã mostra o estado básico da implementação.

> [!NOTE]
> Um exemplo de tempo para o Azure implementar um SQL Server VM: Um teste SQL Server VM abastado na região leste dos EUA com definições padrão leva aproximadamente 12 minutos para ser concluído. Poderá experimentar tempos de implantação mais rápidos ou mais lentos com base na sua região e configurações selecionadas.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a> Abra o VM com desktop remoto

Utilize os seguintes passos para ligar à máquina virtual sql Server com o Protocolo de Ambiente de Trabalho Remoto (RDP):

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Depois de ligar à máquina virtual do SQL Server, pode iniciar o SQL Server Management Studio e estabelecer ligação à Autenticação do Windows com as suas credenciais de administrador local. Se ativou a Autenticação do SQL Server, também pode ligar com Autenticação do SQL Server com o início de sessão do SQL e a palavra-passe que configurou durante o aprovisionamento.

O acesso ao computador permite-lhe alterar diretamente as definições do SQL Server e da máquina com base nos seus requisitos. Por exemplo, pode configurar as definições da firewall ou alterar as definições de configuração do SQL Server.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a> Ligue-se ao SQL Server remotamente

Nesta passagem, selecionou o acesso **público** para a máquina virtual e **a Autenticação do Servidor SQL.** Estas definições configuraram automaticamente a máquina virtual para permitir ligações ao SQL Server a partir de qualquer cliente através da Internet (partindo do princípio de que tem o início de sessão SQL correto).

> [!NOTE]
> Se não selecionou Público durante o aprovisionamento, pode alterar as definições de conectividade SQL através do portal após o aprovisionamento. Para obter mais informações, veja o artigo [Alterar as definições de conectividade SQL](ways-to-connect-to-sql.md#change).

As seguintes secções mostram como ligar através da internet à sua instância VM do SQL Server.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > Este exemplo utiliza a porta comum 1433. No entanto, este valor terá de ser modificado se uma porta diferente (como 1401) tiver sido especificada durante a implantação do SQL Server VM. 


## <a name="next-steps"></a>Passos seguintes

Para outras informações sobre como utilizar o SQL Server no Azure, consulte o artigo [SQL Server em Virtual Machines do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md) e as [Perguntas Mais Frequentes](frequently-asked-questions-faq.md).