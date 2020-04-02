---
title: Avaliar VMs VMware para migração para o Azure
description: Descreve como avaliar os VMs no local para migração para Azure usando a Avaliação do Servidor Migratório Azure.
ms.topic: tutorial
ms.date: 03/23/2019
ms.openlocfilehash: 944b7c12a353a29a172576974261eece63ebf668
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548749"
---
# <a name="assess-vmware-vms-by-using-azure-migrate-server-assessment"></a>Avaliar VMware VMs utilizando a avaliação do servidor migratório Azure

Este artigo mostra-lhe como avaliar no local máquinas virtuais VMware (VMs), utilizando a ferramenta de avaliação de [migração de migrantes Azure:Server.](migrate-services-overview.md#azure-migrate-server-assessment-tool)


Este tutorial é o segundo de uma série que demonstra como avaliar e migrar VMware VMs para Azure. Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um projeto Azure Migrate.
> * Instale um aparelho Azure Migrate que funciona no local para avaliar os VMs.
> * Inicie a descoberta contínua de VMs no local. O aparelho envia dados de configuração e desempenho para VMs descobertos para o Azure.
> * O grupo descobriu vMs e avaliou o grupo VM.
> * Reveja a avaliação.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa rapidamente configurar uma prova de conceito. Os tutoriais usam opções padrão sempre que possível, e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, reveja os artigos de como fazer.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- [Complete o primeiro tutorial](tutorial-prepare-vmware.md) desta série. Se não o fizeres, as instruções neste tutorial não funcionarão.
- Eis o que deviater feito no primeiro tutorial:
    - [Prepare Azure](tutorial-prepare-vmware.md#prepare-azure) para trabalhar com a Azure Migrate.
    - [Prepare a VMware para avaliação](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) para avaliação. Isto inclui verificar as definições de VMware, configurar uma conta que o Azure Migrate pode usar para aceder ao VCenter Server.
    - [Verifique](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment) o que precisa para utilizar o aparelho Azure Migrate para avaliação de VMware.

## <a name="set-up-an-azure-migrate-project"></a>Criar um projeto Azure Migrate

Criar um novo projeto Azure Migrate da seguinte forma:

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
1. Em **Serviços**, selecione **Azure Migrate**.
1. Em **visão geral**, em **Discover, avalie e emigra os servidores,** selecione **avaliar e migrar servidores**.

   ![Botão para avaliar e migrar servidores](./media/tutorial-assess-vmware/assess-migrate.png)

1. Em **Getting started,** selecione **Adicionar ferramentas**.
1. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.     
1. Em Detalhes do **Projeto,** especifique o nome do projeto e a geografia em que pretende criar o projeto. A Ásia, a Europa, o Reino Unido e os Estados Unidos são apoiados.

   A geografia do projeto só é utilizada para armazenar os metadados recolhidos das VMs no local. Pode selecionar qualquer região de destino ao executar uma migração.

   ![Caixas para nome e região do projeto](./media/tutorial-assess-vmware/migrate-project.png)

1. Selecione **Next**.
1. Na **ferramenta de avaliação Select,** selecione **Azure Migrate: Server Assessment** > **Next**.

   ![Seleção para a ferramenta de avaliação do servidor](./media/tutorial-assess-vmware/assessment-tool.png)

1. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
1. Em **Rever + adicionar ferramentas,** reveja as definições e selecione Adicionar **ferramentas**.
1. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Será encaminhado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.

## <a name="set-up-the-azure-migrate-appliance"></a>Instale o aparelho Migratório Azure

Azure Migrate:Server Assessment usa um aparelho ligeiro de migração Azure. O aparelho realiza a descoberta de VM e envia metadados VM e dados de desempenho para a Migração Azure.
- O aparelho pode ser configurado num VMware VM utilizando um modelo OVA descarregado. Em alternativa, pode configurar o aparelho numa VM ou numa máquina física com um script de instalação PowerShell.
- Este tutorial usa o modelo OVA. Reveja [este artigo](deploy-appliance-script.md) se quiser configurar o aparelho com um script.

Depois de criar o aparelho, verifique se pode ligar-se à Avaliação do Servidor Azure Migrate:Server, configurá-lo pela primeira vez e registá-lo com o projeto Azure Migrate.



### <a name="download-the-ova-template"></a>Descarregue o modelo OVA

1. Em **Objetivos** > de Migração**Servidores** > **Azure Migrar: Avaliação do servidor**, selecione **Discover**.
1. Em **Discover machines** > **As suas máquinas estão virtualizadas?** **Yes, with VMWare vSphere hypervisor**
1. Selecione **Baixar** para descarregar o ficheiro do modelo OVA.

   ![Seleções para descarregar um ficheiro OVA](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Verificar a segurança

Verifique se o ficheiro OVA está seguro, antes de o implementar:

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
1. Executar o seguinte comando para gerar o haxixe para o ficheiro OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

Para a versão 2.19.07.30, o hash gerado deve corresponder a estes valores:

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c

### <a name="create-the-appliance-vm"></a>Crie o vm do aparelho

Importar o ficheiro descarregado e criar um VM:

1. Na consola vSphere Client, selecione Modelo**OVF de implementação**de **ficheiros** > .

   ![Comando de menu para implantação de um modelo OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

1. No assistente de modelo de implante OVF > **Fonte,** especifique a localização do ficheiro OVA.
1. Em **Nome** e **Localização,** especifique um nome amigável para o VM. Selecione o objeto de inventário no qual o VM será hospedado.
1. No **hospedeiro/cluster,** especifique o hospedeiro ou o cluster em que o VM irá funcionar.
1. No **Armazenamento,** especifique o destino de armazenamento para o VM.
1. Em **Formato do Disco**, especifique o tipo e o tamanho do disco.
1. No **Mapeamento da Rede,** especifique a rede à qual o VM irá ligar. A rede precisa de conectividade de internet para enviar metadados para a Avaliação do Servidor Migratório Azure.
1. Reveja e confirme as definições e, em seguida, selecione **Terminar**.

### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o VM do aparelho pode ligar-se a [URLs Azure](migrate-appliance.md#url-access).

### <a name="configure-the-appliance"></a>Configure o aparelho

Instale o aparelho pela primeira vez.

> [!NOTE]
> Se configurar o aparelho utilizando um [script PowerShell](deploy-appliance-script.md) em vez do OVA descarregado, os dois primeiros passos deste procedimento não são relevantes.

1. Na consola vSphere Client, clique no VM e, em seguida, selecione **Open Console**.
1. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
1. Abra um navegador em qualquer máquina que possa ligar-se ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho selecionando o atalho da aplicação.
1. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
   - **Licença**: Aceite os termos da licença e leia as informações de terceiros.
   - **Conectividade**: A aplicação verifica se o VM tem acesso à Internet. Se o VM utilizar um proxy:
     - Selecione **definições de Proxy**, e http://ProxyIPAddress especifique o endereço proxy e a porta de escuta no formulário ou http://ProxyFQDN.
     - Especifique as credenciais se o proxy precisar de autenticação.
     - Note que apenas http proxy é suportado.
   - **Sincronização de tempo**: O tempo no aparelho deve estar sincronizado com o tempo de internet para que a descoberta funcione corretamente.
   - **Instalar atualizações**: O aparelho garante que as últimas atualizações estão instaladas.
   - **Instalação VDDK**: O aparelho verifica se o VMWare vSphere Virtual Disk Development Kit (VDDK) está instalado. Se não estiver instalado, baixe o VDDK 6.7 da VMware e extraa o conteúdo de zip descarregado para o local especificado no aparelho.

     A Migração do Servidor Migratório Azure usa o VDDK para replicar máquinas durante a migração para O Azure.       

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Selecione **Iniciar sessão**. Se não aparecer, certifique-se de que desativou o bloqueador pop-up no navegador.
1. No novo separador, inscreva-se utilizando o seu nome de utilizador E senha Azure.
   
   O sessão com um PIN não é suportado.
1. Depois de iniciar sessão com sucesso, volte para a aplicação web.
1. Selecione a subscrição em que o projeto Azure Migrate foi criado e, em seguida, selecione o projeto.
1. Especifique um nome para o aparelho. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Selecione **Registar**.


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

O aparelho precisa de se ligar ao VCenter Server para descobrir os dados de configuração e desempenho dos VMs.

### <a name="specify-vcenter-server-details"></a>Especificar detalhes do vCenter Server
1. Em especificar os detalhes do **servidor vCenter,** especifique o nome (FQDN) ou o endereço IP da instância do servidor vCenter. Pode deixar a porta predefinida ou especificar uma porta personalizada na qual o VCenter Server ouve.
2. No **nome do utilizador** e na **palavra-passe,** especifique as credenciais de conta vCenter Server que o aparelho utilizará para descobrir VMs na instância vCenter Server. 

    - Deveria ter criado uma conta com as permissões necessárias no [tutorial anterior.](tutorial-prepare-vmware.md#set-up-an-account-for-assessment)
    - Se pretender analisar a descoberta de objetos VMware específicos (centros de dados vCenter Server, clusters, uma pasta de clusters, anfitriões, uma pasta de anfitriões ou VMs individuais),reveja as instruções [deste artigo](set-discovery-scope.md) para restringir a conta utilizada pela Azure Migrate.

3. Selecione **validate a ligação** para se certificar de que o aparelho pode ligar-se ao VCenter Server.
4. No **Discover aplicações e dependências em VMs,** clique opcionalmente em **Adicionar credenciais,** e especifique o sistema operativo para o qual as credenciais são relevantes, e o nome de utilizador e senha de credenciais. Em seguida, clique em **Adicionar**..

    - Você opcionalmente adiciona credenciais aqui se criou uma conta para usar para a funcionalidade de descoberta de [aplicações](how-to-discover-applications.md), ou a funcionalidade de análise de [dependência sem agente](how-to-create-group-machine-dependencies-agentless.md).
    - Se não estiver a utilizar estas funcionalidades, pode saltar esta definição.
    - Reveja as credenciais necessárias para a descoberta de [apps](migrate-support-matrix-vmware.md#application-discovery), ou para [análise sem agente.](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)

5. **Salve e comece**a descoberta, para dar início à descoberta vm.

A descoberta funciona da seguinte forma:
- Leva cerca de 15 minutos para que os metadados VM descobertos apareçam no portal.
- A descoberta de aplicações, funções e funcionalidades instaladas leva algum tempo. A duração depende do número de VMs descobertos. Para 500 VMs, leva aproximadamente uma hora para que o inventário de aplicações apareça no portal Azure Migrate.

### <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Após a descoberta, pode verificar se os VMs aparecem no portal Azure:

1. Abra o painel de migração Azure.
1. Em **Azure Migrate - Servers** > **Azure Migrate: Servers Assessment**, selecione o ícone que apresenta a contagem para **servidores descobertos**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Pode criar dois tipos de avaliações utilizando a Avaliação do Servidor Migrador De Azure:

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações baseadas em dados de desempenho recolhidos | **Tamanho VM recomendado**: Baseado em CPU e dados de utilização da memória.<br/><br/> **Tipo de disco recomendado (disco gerido padrão ou premium)**: Baseado no IOPS e na entrada dos discos no local.
**Como no local** | Avaliações baseadas no dimensionamento no local | **Tamanho VM recomendado**: Baseado no tamanho VM no local.<br/><br> **Tipo**de disco recomendado : Com base na definição do tipo de armazenamento que selecionar para a avaliação.

## <a name="run-an-assessment"></a>Executar uma avaliação

Eexecutar uma avaliação da seguinte forma:

1. Reveja as [melhores práticas](best-practices-assessment.md) para a criação de avaliações.
1. No separador **Servidores,** no **Azure Migrate: Server Assessment** tile, selecione **Avaliar**.

   ![Localização do botão Avaliar](./media/tutorial-assess-vmware/assess.png)

1. Em **avaliar os servidores,** especifique um nome para a avaliação.
1. Selecione **Ver tudo**e, em seguida, rever as propriedades de avaliação.

   ![Propriedades de avaliação](./media/tutorial-assess-vmware/view-all.png)

1. Em **Selecionar ou criar um grupo,** selecione Criar **Novo**e especifique um nome de grupo. Um grupo reúne um ou mais VMs para avaliação.
1. Em **Adicionar máquinas ao grupo,** selecione VMs para adicionar ao grupo.
1. Selecione **Criar Avaliação** para criar o grupo e executar a avaliação.

   ![Avaliar servidores](./media/tutorial-assess-vmware/assessment-create.png)

1. Após a criação da avaliação, veja-a em **Servers** > **Azure Migrate:** > **Avaliações**de Avaliação do Servidor .
1. Selecione **avaliação de Exportação** para descarregá-lo como um ficheiro Excel.

## <a name="review-an-assessment"></a>Rever uma avaliação

Uma avaliação descreve:

- **Prontidão azure**: Se os VMs são adequados para migração para Azure.
- **Estimativa mensal de custos**: Os custos estimados mensais de cálculo e armazenamento para a execução dos VMs em Azure.
- **Estimativa mensal**do custo do armazenamento : Custos estimados para armazenamento de disco após migração.

Para visualizar uma avaliação:

1. Em **objetivos** > de migração**Servidores**, selecione **Avaliações** em **Azure Migrate: Server Assessment**.
1. Em **Avaliações,** selecione uma avaliação para abri-la.

   ![Resumo da avaliação](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Rever a prontidão do Azure

1. Na **prontidão de Azure,** verifique se os VMs estão prontos para a migração para Azure.
1. Reveja o estado vm:
    - **Pronto para Azure**: Utilizado quando a Azure Migrate recomenda uma estimativa de tamanho vm e custopara os VMs na avaliação.
    - **Pronto com condições**: Mostra problemas e sugeriu reparação.
    - **Não está pronto para o Azure**: Mostra questões e sugeriu reparação.
    - **Prontidão desconhecida**: Usado quando o Azure Migrate não consegue avaliar a prontidão devido a problemas de disponibilidade de dados.

1. Selecione um estado de **prontidão Azure.** Pode ver detalhes de prontidão vm. Também pode perfurar para ver detalhes vm, incluindo definições de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Analisar detalhes de custos

O resumo da avaliação mostra o cálculo estimado e o custo de armazenamento de VMs de funcionamento em Azure. Os custos são agregados para todos os VMs do grupo avaliado. Você pode perfurar para ver detalhes de custos para VMs específicos.

> [!NOTE]
> As estimativas de custos baseiam-se nas recomendações de tamanho para uma máquina, seus discos e suas propriedades. As estimativas são para executar os VMs no local como VMs IaaS. A Avaliação do Servidor Migratório Azure não considera os custos do PaaS ou do SaaS.

Os custos de armazenamento agregados para o grupo avaliado são divididos em diferentes tipos de discos de armazenamento. 

### <a name="review-confidence-rating"></a>Rever a classificação de confiança

A Avaliação do Servidor Migrado Azure atribui uma classificação de confiança a uma avaliação baseada no desempenho, de 1 estrela (mais baixa) a 5 estrelas (mais alta).

![Classificação de confiança](./media/tutorial-assess-vmware/confidence-rating.png)

A classificação de confiança ajuda-o a estimar a fiabilidade das recomendações de tamanho da avaliação. A classificação baseia-se na disponibilidade de pontos de dados necessários para calcular a avaliação:

**Disponibilidade de pontos de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas

[Conheça as melhores práticas](best-practices-assessment.md#best-practices-for-confidence-ratings) para avaliações de confiança.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, instale um aparelho Azure Migrate. Também criou e reviu uma avaliação.

Para aprender a migrar VMware VMs para Azure utilizando a Migração do Servidor Migratório Migratório Migratório Migratório Azure Migrate, continue até ao terceiro tutorial da série:

> [!div class="nextstepaction"]
> [Migrar VMs VMware](./tutorial-migrate-vmware.md)
