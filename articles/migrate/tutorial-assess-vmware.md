---
title: Avaliar VMware VMs para migração para Azure usando a Avaliação do Servidor Migratório Azure
description: Descreve como avaliar os VMs no local para migração para Azure utilizando o Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: hamusa
ms.openlocfilehash: 7f161afe13bad8c548806d4b4ceb9372dc511cc3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388991"
---
# <a name="assess-vmware-vms-by-using-azure-migrate-server-assessment"></a>Avaliar VMware VMs utilizando a avaliação do servidor migratório Azure

Este artigo mostra-lhe como avaliar no local máquinas virtuais VMware (VMs) utilizando a ferramenta de avaliação do servidor em Azure Migrate.

[A Azure Migrate](migrate-services-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar apps, infraestruturas e cargas de trabalho para o Microsoft Azure. O hub inclui ferramentas Azure Migrate e ofertas independentes de fornecedores de software (ISV) de parceiros da Microsoft.

Este tutorial é o segundo de uma série que demonstra como avaliar e migrar VMware VMs para Azure. Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar um projeto Azure Migrate.
> * Instale um aparelho Azure Migrate que funciona no local para avaliar os VMs.
> * Inicie a descoberta contínua de VMs no local. O aparelho envia dados de configuração e desempenho para VMs descobertos para o Azure.
> * O grupo descobriu vMs e avaliou o grupo VM.
> * Reveja a avaliação.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa rapidamente configurar uma prova de conceito. Os tutoriais usam opções padrão sempre que possível, e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, reveja os artigos de como fazer.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Complete o primeiro tutorial](tutorial-prepare-vmware.md) desta série. Se não o fizeres, as instruções neste tutorial não funcionarão.

Eis o que deviater feito no primeiro tutorial:

- [Instale permissões Azure](tutorial-prepare-vmware.md#prepare-azure) para a Migração Azure.
- [Prepare a VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) para avaliação:
   - [Verificar](migrate-support-matrix-vmware.md#vmware-requirements) Definições vMware.
   - Configurar permissões em VMware, para criar um VMware VMm com um modelo OVA.
   - Criar uma conta para a descoberta de [VM.](migrate-support-matrix-vmware.md#vmware-requirements) 
   - Disponibilizar as [portas necessárias.](migrate-support-matrix-vmware.md#port-access)
   - Esteja atento aos [URLs necessários](migrate-replication-appliance.md#url-access) para o acesso ao Azure.

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

1. Selecione **Seguinte**.
1. Na **ferramenta de avaliação Select**, selecione **Azure Migrate: Avaliação do servidor** > **Seguinte**.

   ![Seleção para a ferramenta de avaliação do servidor](./media/tutorial-assess-vmware/assessment-tool.png)

1. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
1. Em **Rever + adicionar ferramentas,** reveja as definições e selecione Adicionar **ferramentas**.
1. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Será encaminhado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.

## <a name="set-up-the-appliance-vm"></a>Configurar o vm do aparelho

A Avaliação do Servidor Migratório Azure executa um aparelho VMware VM leve. Este aparelho realiza a descoberta de VM e recolhe metadados VM e dados de desempenho.

Para configurar o aparelho, o aparelho:

- Descarregue um ficheiro de modelo OVA e importe-o para vCenter Server.
- Crie o aparelho e verifique se pode ligar-se à Avaliação do Servidor Migratório Azure.
- Configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate.

Pode configurar vários aparelhos para um único projeto Azure Migrate. Em todos os aparelhos, a Server Assessment suporta a descoberta de até 35.000 VMs. Pode descobrir um máximo de 10.000 servidores por aparelho.

### <a name="download-the-ova-template"></a>Descarregue o modelo OVA

1. Em **Objetivos de migração** > **servidores** > **Azure Migrate: Avaliação do servidor**, selecione **Discover**.
1. Em **Discover machines** > **As suas máquinas estão virtualizadas?**
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

1. Na consola vSphere Client, selecione **File** > **Implementar modelo OVF**.

   ![Comando de menu para implantação de um modelo OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

1. No modelo de implantação do MODELO OVF Assistente > **Fonte,** especifique a localização do ficheiro OVA.
1. Em **Nome** e **Localização,** especifique um nome amigável para o VM. Selecione o objeto de inventário no qual o VM será hospedado.
1. No **hospedeiro/cluster,** especifique o hospedeiro ou o cluster em que o VM irá funcionar.
1. No **Armazenamento,** especifique o destino de armazenamento para o VM.
1. Em **Formato do Disco**, especifique o tipo e o tamanho do disco.
1. No **Mapeamento da Rede,** especifique a rede à qual o VM irá ligar. A rede precisa de conectividade de internet para enviar metadados para a Avaliação do Servidor Migratório Azure.
1. Reveja e confirme as definições e, em seguida, selecione **Terminar**.

### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o VM do aparelho pode ligar-se a [URLs Azure](migrate-appliance.md#url-access).

### <a name="configure-the-appliance"></a>Configure o aparelho

Instale o aparelho utilizando os seguintes passos:

1. Na consola vSphere Client, clique no VM e, em seguida, selecione **Open Console**.
1. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
1. Abra um navegador em qualquer máquina que possa ligar-se ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho selecionando o atalho da aplicação.
1. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
   - **Licença**: Aceite os termos da licença e leia as informações de terceiros.
   - **Conectividade**: A aplicação verifica se o VM tem acesso à Internet. Se o VM utilizar um proxy:
     - Selecione **as definições de Proxy**e especifique o endereço proxy e a porta de escuta sob a forma http://ProxyIPAddress ou http://ProxyFQDN.
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
1. No **nome do utilizador** e na **palavra-passe,** especifique as credenciais de conta vCenter Server que o aparelho utilizará para descobrir VMs na instância vCenter Server. 

   Certifique-se de que a conta tem as [permissões necessárias para a descoberta.](migrate-support-matrix-vmware.md#vmware-requirements) Pode [analisar a descoberta](tutorial-assess-vmware.md#set-the-scope-of-discovery) limitando o acesso à conta vCenter.
1. Selecione **validate a ligação** para se certificar de que o aparelho pode ligar-se ao VCenter Server.

### <a name="specify-vm-credentials"></a>Especificar credenciais VM
Para a descoberta de aplicações, funções e funcionalidades, e para visualizar dependências dos VMs, pode fornecer credenciais VM que têm acesso aos VMware VMs. Pode adicionar uma credencial para VMs windows e uma credencial para VMs Linux. [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) sobre as permissões de acesso necessárias.

> [!NOTE]
> Esta entrada é opcional, mas você precisa dela se quiser ativar a descoberta da aplicação e visualização de dependência sem agente.

1. Em **Discover aplicações e dependências em VMs,** selecione **Adicionar credenciais.**
1. Faça uma seleção para **sistema operativo.**
1. Forneça um nome amigável para a credencial.
1. No **Username** e **Password,** especifique uma conta que tenha pelo menos acesso ao hóspede nos VMs.
1. Selecione **Adicionar**.

Depois de especificar a instância vCenter Server e as credenciais VM (opcional), selecione **Save e comece** a descoberta para começar a descoberta do ambiente no local.

Leva cerca de 15 minutos para que os metadados de VMs descobertos apareçam no portal. A descoberta de aplicações, funções e funcionalidades instaladas leva algum tempo. A duração depende do número de VMs descobertos. Para 500 VMs, leva aproximadamente 1 hora para que o inventário de aplicações apareça no portal Azure Migrate.

### <a name="set-the-scope-of-discovery"></a>Definir o âmbito da descoberta

A descoberta pode ser monitorizada limitando o acesso da conta vCenter que é usada para ser descoberta. Pode definir o âmbito para centros de dados vCenter Server, clusters, uma pasta de clusters, anfitriões, uma pasta de anfitriões ou VMs individuais.

Para definir o âmbito, execute os seguintes procedimentos.

#### <a name="1-create-a-vcenter-user-account"></a>1. Criar uma conta de utilizador vCenter
1.  Inicie sessão no VSphere Web Client como administrador do VCenter Server.
1.  Selecione **Administration** > **utilizadores e Grupos SSO**, e, em seguida, selecione o separador **Utilizadores.**
1.  Selecione o ícone **Novo Utilizador.**
1.  Preencha as informações necessárias para criar um novo utilizador e, em seguida, selecione **OK**.

#### <a name="2-define-a-new-role-with-required-permission"></a>2. Definir um novo papel com permissão necessária
Este procedimento é necessário para a migração sem agente do servidor.
1.  Inicie sessão no VSphere Web Client como administrador do VCenter Server.
1.  Navegue na **Administração** > **Role Manager**.
1.  Selecione a sua instância vCenter Server a partir do menu suspenso.
1.  Selecione **Criar função**.
1.  Introduza um nome para o novo papel (como <em>Azure_Migrate).</em>
1.  Atribuir [permissões](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) ao papel recém-definido.
1.  Selecione **OK**.

#### <a name="3-assign-permissions-on-vcenter-objects"></a>3. Atribuir permissões em objetos vCenter

Existem duas abordagens para atribuir permissões em objetos de inventário no vCenter para a conta de utilizador vCenter com uma função atribuída à sua.

Para a Avaliação do Servidor, deve aplicar a função **de Leitura apenas** na conta de utilizador vCenter para todos os objetos-mãe onde os VMs a serem descobertos estão hospedados. Todos os objetos-mãe serão incluídos: anfitrião, pasta de anfitriões, cluster e pasta de clusters na hierarquia até ao datacenter. Estas permissões serão propagadas a objetos infantis na hierarquia.

Da mesma forma para a Migração do Servidor, deve aplicar uma função definida pelo utilizador com [permissões](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware) à conta de utilizador vCenter para todos os objetos-mãe onde os VMs a migrar são hospedados. Este papel pode ser chamado <em>De _Migrate.</em>

![Atribuir permissões](./media/tutorial-assess-vmware/assign-perms.png)

A abordagem alternativa é atribuir a conta do utilizador e o papel ao nível do datacenter e propagá-los aos objetos infantis. Em seguida, dê à conta uma função **de não acesso** para cada objeto (como um VM) que você não quer descobrir/migrar. 

Esta configuração alternativa é complicada. Expõe controlos de acesso acidentais, porque cada novo objeto infantil também é automaticamente concedido acesso que é herdado do progenitor. Por isso, recomendamos que utilize a primeira abordagem.

> [!NOTE]
> Atualmente, a Avaliação do Servidor não pode descobrir VMs se a conta vCenter tiver acesso concedido ao nível da pasta VCenter VM. Se quiser analisar a sua descoberta através de pastas VM, pode fazê-lo utilizando o seguinte procedimento. Garante que a conta vCenter tem acesso apenas de leitura atribuído a um nível VM.
>
> 1. Atribuir permissões de leitura apenas em todos os VMs nas pastas VM às quais pretende examinar a descoberta.
> 1. Conceder acesso apenas a todos os objetos-mãe onde os VMs estão hospedados. Todos os objetos-mãe (anfitrião, pasta de anfitriões, cluster, pasta de clusters) na hierarquia até ao datacenter serão incluídos. Não precisas de propagar as permissões a todos os objetos infantis.
> 1. Utilize as credenciais para descoberta selecionando o datacenter como Âmbito de **Recolha**. O controlo de acesso baseado em funções que é criado garante que o utilizador vCenter correspondente terá acesso apenas a VMs específicos do inquilino.
>
> Note que as pastas de anfitriões e agrupamentos são suportadas.

### <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Após a descoberta, pode verificar se os VMs aparecem no portal Azure:

1. Abra o painel de migração Azure.
1. Em **Azure Migrate - Servidores** > **Azure Migrate: Avaliação**do servidor , selecione o ícone que apresenta a contagem para **servidores descobertos**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Pode criar dois tipos de avaliações utilizando a Avaliação do Servidor Migrador De Azure:

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações baseadas em dados de desempenho recolhidos | **Tamanho VM recomendado**: Baseado em CPU e dados de utilização da memória.<br/><br/> **Tipo de disco recomendado (disco gerido padrão ou premium)** : Baseado no IOPS e na entrada dos discos no local.
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

1. Após a criação da avaliação, veja-a nos **Servidores** > **Azure Migrate: Avaliação** do servidor > **Avaliações**.
1. Selecione **avaliação de Exportação** para descarregá-lo como um ficheiro Excel.

## <a name="review-an-assessment"></a>Rever uma avaliação

Uma avaliação descreve:

- **Prontidão azure**: Se os VMs são adequados para migração para Azure.
- **Estimativa mensal de custos**: Os custos estimados mensais de cálculo e armazenamento para a execução dos VMs em Azure.
- **Estimativa mensal**do custo do armazenamento : Custos estimados para armazenamento de disco após migração.

Para visualizar uma avaliação:

1. Nos **objetivos de migração** > **Servidores**, selecione **Avaliações** em **Azure Migrate: Server Assessment**.
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
> [VMs migratórios VMware](./tutorial-migrate-vmware.md)
