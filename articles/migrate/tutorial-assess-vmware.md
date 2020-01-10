---
title: Avaliar as VMs do VMware para migração para o Azure usando a avaliação do servidor de migrações para Azure
description: Descreve como avaliar as VMs do VMware locais para migração para o Azure usando as migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: hamusa
ms.openlocfilehash: 31af4ad9c6985202555dbcbe86c52e45d5c4154a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453288"
---
# <a name="assess-vmware-vms-by-using-azure-migrate-server-assessment"></a>Avaliar VMs do VMware usando a avaliação do servidor de migrações para Azure

Este artigo mostra como avaliar as VMs (máquinas virtuais) VMware locais usando a ferramenta de avaliação do servidor nas migrações para Azure.

As [migrações para Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam a descobrir, avaliar e migrar aplicativos, infraestrutura e cargas de trabalho para Microsoft Azure. O Hub inclui as ferramentas de migração do Azure e as ofertas de fornecedores independentes de software (ISV) dos parceiros da Microsoft.

Este tutorial é o segundo de uma série que demonstra como avaliar e migrar VMs do VMware para o Azure. Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configure um projeto de migrações para Azure.
> * Configure um dispositivo de migrações para Azure que é executado localmente para avaliar as VMs.
> * Inicie a descoberta contínua de VMs locais. O dispositivo envia dados de desempenho e de configuração para VMs descobertas no Azure.
> * Agrupe as VMs descobertas e avalie o grupo de VMs.
> * Examine a avaliação.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário, para que você possa configurar rapidamente uma prova de conceito. Os tutoriais usam as opções padrão sempre que possível e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, revise os artigos de instruções.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Conclua o primeiro tutorial](tutorial-prepare-vmware.md) desta série. Caso contrário, as instruções neste tutorial não funcionarão.

Veja o que você deve ter feito no primeiro tutorial:

- [Configure as permissões do Azure](tutorial-prepare-vmware.md#prepare-azure) para migrações para Azure.
- [Preparar o VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) para avaliação:
   - Verifique as configurações do VMware.
   - Configure permissões para criar uma VM VMware com um modelo OVA.
   - Configure uma conta para descoberta de VM. 
   - Tornar as portas necessárias disponíveis.
   - Lembre-se das URLs necessárias para o acesso ao Azure.

## <a name="set-up-an-azure-migrate-project"></a>Configurar um projeto de migrações para Azure

Configure um novo projeto de migrações para Azure da seguinte maneira:

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
1. Em **Serviços**, selecione **Azure Migrate**.
1. Em **visão geral**, em **descobrir, avaliar e migrar servidores**, selecione **avaliar e migrar servidores**.

   ![Botão para avaliar e migrar servidores](./media/tutorial-assess-vmware/assess-migrate.png)

1. Em **introdução**, selecione **Adicionar ferramentas**.
1. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.     
1. Em **detalhes do projeto**, especifique o nome do projeto e a geografia em que você deseja criar o projeto. Há suporte para a Ásia, Europa, Reino Unido e Estados Unidos.

   A geografia do projeto só é utilizada para armazenar os metadados recolhidos das VMs no local. Pode selecionar qualquer região de destino ao executar uma migração.

   ![Caixas para nome e região do projeto](./media/tutorial-assess-vmware/migrate-project.png)

1. Selecione **Seguinte**.
1. Em **selecionar ferramenta de avaliação**, selecione **migrações para Azure: avaliação do servidor** > **Avançar**.

   ![Seleção para a ferramenta de avaliação do servidor](./media/tutorial-assess-vmware/assessment-tool.png)

1. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
1. Em **examinar + adicionar ferramentas**, examine as configurações e selecione **Adicionar ferramentas**.
1. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Será encaminhado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.

## <a name="set-up-the-appliance-vm"></a>Configurar a VM do dispositivo

A avaliação de servidor de migrações para Azure executa um dispositivo leve de VM VMware. Esse dispositivo executa a descoberta de VM e reúne metadados de VM e dados de desempenho.

Para configurar o dispositivo, você:

- Baixe um arquivo de modelo OVA e importe-o para vCenter Server.
- Crie o dispositivo e verifique se ele pode se conectar à avaliação do servidor de migrações para Azure.
- Configure o dispositivo pela primeira vez e registre-o com o projeto de migrações para Azure.

Você pode configurar vários dispositivos para um único projeto de migrações para Azure. Em todos os dispositivos, a avaliação do servidor dá suporte à descoberta de até 35.000 VMs. Ele pode descobrir um máximo de 10.000 servidores por dispositivo.

### <a name="download-the-ova-template"></a>Baixar o modelo OVA

1. Em **metas de migração** > **servidores** > **migrações para Azure: avaliação do servidor**, selecione **descobrir**.
1. Em **descobrir computadores** > **são seus computadores virtualizados?** , selecione **Sim, com o hipervisor do VMware vSphere**.
1. Selecione **baixar** para baixar o arquivo de modelo ova.

   ![Seleções para baixar um arquivo OVA](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Verificar segurança

Verifique se o arquivo OVA é seguro, antes de implantá-lo:

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
1. Execute o seguinte comando para gerar o hash para o arquivo OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

Para a versão 2.19.07.30, o hash gerado deve corresponder a estes valores:

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | c06ac2a2c0f870d3b274a0b7a73b78b1
SHA256 | 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c

### <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie uma VM:

1. No console do cliente do vSphere, selecione **arquivo** > **implantar o modelo de OVF**.

   ![Comando de menu para implantar um modelo OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

1. No Assistente para implantar modelo de OVF > **origem**, especifique o local do arquivo ova.
1. Em **nome** e **local**, especifique um nome amigável para a VM. Selecione o objeto de inventário no qual a VM será hospedada.
1. Em **host/cluster**, especifique o host ou cluster no qual a VM será executada.
1. Em **armazenamento**, especifique o destino de armazenamento para a VM.
1. Em **Formato do Disco**, especifique o tipo e o tamanho do disco.
1. Em **mapeamento de rede**, especifique a rede à qual a VM será conectada. A rede precisa de conectividade com a Internet para enviar metadados para a avaliação do servidor de migrações para Azure.
1. Examine e confirme as configurações e, em seguida, selecione **concluir**.

### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às [URLs do Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).

### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo usando as seguintes etapas:

1. No console do cliente do vSphere, clique com o botão direito do mouse na VM e selecione **Abrir console**.
1. Forneça o idioma, o fuso horário e a senha para o dispositivo.
1. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo selecionando o atalho do aplicativo.
1. No aplicativo Web > **configurar os pré-requisitos**, faça o seguinte:
   - **Licença**: aceite os termos de licença e leia as informações de terceiros.
   - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
     - Selecione **configurações de proxy**e especifique o endereço de proxy e a porta de escuta no formato http://ProxyIPAddress ou http://ProxyFQDN.
     - Especifique as credenciais se o proxy precisar de autenticação.
     - Observe que somente o proxy HTTP tem suporte.
   - **Sincronização de horário**: o tempo no dispositivo deve ser sincronizado com a hora da Internet para que a descoberta funcione corretamente.
   - **Instalar atualizações**: o dispositivo garante que as atualizações mais recentes sejam instaladas.
   - **Instalar o VDDK**: o dispositivo verifica se o VDDK (Kit de desenvolvimento de disco virtual) do VMware vSphere está instalado. Se ele não estiver instalado, baixe o VDDK 6,7 do VMware e extraia o conteúdo do zip baixado para o local especificado no dispositivo.

     A migração de servidor de migrações para Azure usa o VDDK para replicar máquinas durante a migração para o Azure.       

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo com as migrações para Azure

1. Selecione **fazer logon**. Se não aparecer, verifique se você desabilitou o bloqueador de pop-ups no navegador.
1. Na guia novo, entre usando seu nome de usuário e senha do Azure.
   
   Não há suporte para a entrada com um PIN.
1. Depois de entrar com êxito, volte para o aplicativo Web.
1. Selecione a assinatura na qual o projeto de migração do Azure foi criado e, em seguida, selecione o projeto.
1. Especifique um nome para o dispositivo. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Selecione **Registar**.

## <a name="start-continuous-discovery"></a>Iniciar descoberta contínua

O dispositivo precisa se conectar ao vCenter Server para descobrir a configuração e os dados de desempenho das VMs.

### <a name="specify-vcenter-server-details"></a>Especificar detalhes do vCenter Server
1. Em **especificar vCenter Server detalhes**, especifique o nome (FQDN) ou o endereço IP da instância de vCenter Server. Você pode deixar a porta padrão ou especificar uma porta personalizada na qual o vCenter Server escuta.
1. Em **nome de usuário** e **senha**, especifique as credenciais de conta de vCenter Server que o dispositivo usará para descobrir as VMs na instância de vCenter Server. 

   Verifique se a conta tem as [permissões necessárias para a descoberta](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Você pode fazer [o escopo da descoberta](tutorial-assess-vmware.md#set-the-scope-of-discovery) limitando o acesso à conta do vCenter.
1. Selecione **validar conexão** para garantir que o dispositivo possa se conectar ao vCenter Server.

### <a name="specify-vm-credentials"></a>Especificar credenciais de VM
Para a descoberta de aplicativos, funções e recursos, e para visualizar dependências das VMs, você pode fornecer credenciais de VM que tenham acesso às VMs VMware. Você pode adicionar uma credencial para VMs do Windows e uma credencial para VMs do Linux. [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) sobre as permissões de acesso necessárias.

> [!NOTE]
> Essa entrada é opcional, mas você precisará dela se quiser habilitar a descoberta de aplicativos e a visualização de dependência sem agente.

1. Em **descobrir aplicativos e dependências em VMs**, selecione **Adicionar credenciais**.
1. Faça uma seleção para o **sistema operacional**.
1. Forneça um nome amigável para a credencial.
1. Em **nome de usuário** e **senha**, especifique uma conta que tenha pelo menos acesso de convidado nas VMs.
1. Selecione **Adicionar**.

Depois de especificar a instância de vCenter Server e as credenciais de VM (opcional), selecione **salvar e iniciar a descoberta** para iniciar a descoberta do ambiente local.

Leva cerca de 15 minutos para que os metadados de VMs descobertas apareçam no Portal. A descoberta de aplicativos, funções e recursos instalados leva algum tempo. A duração depende do número de VMs que estão sendo descobertas. Para as VMs 500, leva aproximadamente 1 hora para o inventário de aplicativos aparecer no portal de migrações para Azure.

### <a name="set-the-scope-of-discovery"></a>Definir o escopo da descoberta

A descoberta pode ser delimitada por meio da limitação do acesso da conta do vCenter que é usada para descoberta. Você pode definir o escopo para vCenter Server data centers, clusters, uma pasta de clusters, hosts, uma pasta de hosts ou VMs individuais.

Para definir o escopo, execute os procedimentos a seguir.

#### <a name="1-create-a-vcenter-user-account"></a>1. criar uma conta de usuário do vCenter
1.  Faça logon no cliente Web vSphere como o administrador do vCenter Server.
1.  Selecione **administração** > **usuários e grupos do SSO**e, em seguida, selecione a guia **usuários** .
1.  Selecione o ícone **novo usuário** .
1.  Preencha as informações necessárias para criar um novo usuário e, em seguida, selecione **OK**.

#### <a name="2-define-a-new-role-with-required-permission"></a>2. definir uma nova função com a permissão necessária
Esse procedimento é necessário para a migração de servidor sem agente.
1.  Faça logon no cliente Web vSphere como o administrador do vCenter Server.
1.  Navegue até **administração** > **Gerenciador de funções**.
1.  Selecione sua instância do vCenter Server no menu suspenso.
1.  Selecione **criar função**.
1.  Insira um nome para a nova função (como <em>Azure_Migrate</em>).
1.  Atribua [permissões](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) à função definida recentemente.
1.  Selecione **OK**.

#### <a name="3-assign-permissions-on-vcenter-objects"></a>3. atribuir permissões em objetos do vCenter

Há duas abordagens para atribuir permissões em objetos de inventário no vCenter à conta de usuário do vCenter com uma função atribuída a ele.

Para a avaliação do servidor, você deve aplicar a função **somente leitura** à conta de usuário do vCenter para todos os objetos pai em que as VMs a serem descobertas estão hospedadas. Todos os objetos pai serão incluídos: host, pasta de hosts, cluster e pasta de clusters na hierarquia até o datacenter. Essas permissões serão propagadas para objetos filho na hierarquia.

Da mesma forma, para migração de servidor, você deve aplicar uma função definida pelo usuário com [permissões](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) para a conta de usuário do vCenter para todos os objetos pai onde as VMs a serem migradas estão hospedadas. Essa função pode ser nomeada <em>_Migrate do Azure</em>.

![Atribuir permissões](./media/tutorial-assess-vmware/assign-perms.png)

A abordagem alternativa é atribuir a conta de usuário e a função no nível do datacenter e propagá-las aos objetos filho. Em seguida, dê à conta uma função **sem acesso** para cada objeto (como uma VM) que você não deseja descobrir/migrar. 

Essa configuração alternativa é complicada. Ele expõe controles de acesso acidentais, pois cada novo objeto filho também recebe automaticamente o acesso herdado do pai. Portanto, recomendamos que você use a primeira abordagem.

> [!NOTE]
> Atualmente, a avaliação do servidor não pode descobrir VMs se a conta do vCenter tem acesso concedido no nível da pasta da VM do vCenter. Se você quiser fazer o escopo de sua descoberta por pastas de VM, poderá fazê-lo usando o procedimento a seguir. Ele garante que a conta do vCenter tenha acesso somente leitura atribuído em um nível de VM.
>
> 1. Atribua permissões somente leitura em todas as VMs nas pastas de VM nas quais você deseja definir o escopo da descoberta.
> 1. Conceda acesso somente leitura a todos os objetos pai onde as VMs estão hospedadas. Todos os objetos pai (host, pasta de hosts, cluster, pasta de clusters) na hierarquia até o datacenter serão incluídos. Você não precisa propagar as permissões para todos os objetos filho.
> 1. Use as credenciais para descoberta selecionando o datacenter como **escopo da coleção**. O controle de acesso baseado em função que é configurado garante que o usuário correspondente do vCenter terá acesso somente a VMs específicas de locatário.
>
> Observe que há suporte para as pastas de hosts e clusters.

### <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Após a descoberta, você pode verificar se as VMs aparecem no portal do Azure:

1. Abra o painel migrações para Azure.
1. Em **migrações para Azure-servidores** > **migrações para Azure: avaliação do servidor**, selecione o ícone que exibe a contagem de **servidores descobertos**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Você pode criar dois tipos de avaliações usando a avaliação do servidor de migrações para Azure:

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado em desempenho** | Avaliações com base nos dados de desempenho coletados | **Tamanho de VM recomendado**: com base nos dados de utilização de CPU e memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado Standard ou Premium)** : com base na IOPS e na taxa de transferência dos discos locais.
**Como local** | Avaliações com base no dimensionamento local | **Tamanho de VM recomendado**: com base no tamanho da VM local.<br/><br> **Tipo de disco recomendado**: com base na configuração de tipo de armazenamento que você selecionar para a avaliação.

## <a name="run-an-assessment"></a>Executar uma avaliação

Execute uma avaliação da seguinte maneira:

1. Examine as [práticas recomendadas](best-practices-assessment.md) para a criação de avaliações.
1. Na guia **servidores** , no bloco **migrações para Azure: avaliação do servidor** , selecione **avaliar**.

   ![Local do botão avaliar](./media/tutorial-assess-vmware/assess.png)

1. Em **avaliar servidores**, especifique um nome para a avaliação.
1. Selecione **Exibir tudo**e examine as propriedades de avaliação.

   ![Propriedades da avaliação](./media/tutorial-assess-vmware/view-all.png)

1. Em **selecionar ou criar um grupo**, selecione **criar novo**e especifique um nome de grupo. Um grupo reúne uma ou mais VMs juntas para avaliação.
1. Em **Adicionar computadores ao grupo**, selecione VMs para adicionar ao grupo.
1. Selecione **criar avaliação** para criar o grupo e executar a avaliação.

   ![Avaliar servidores](./media/tutorial-assess-vmware/assessment-create.png)

1. Após a criação da avaliação, exiba-a em **servidores** > **migrações para Azure:** **avaliações de > de**avaliação do servidor.
1. Selecione **Exportar avaliação** para baixá-lo como um arquivo do Excel.

## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação descreve:

- **Preparação do Azure**: se as VMs são adequadas para a migração para o Azure.
- **Estimativa de custo mensal**: a computação mensal estimada e os custos de armazenamento para executar as VMs no Azure.
- **Estimativa de custo de armazenamento mensal**: custos estimados para armazenamento em disco após a migração.

Para exibir uma avaliação:

1. Em **metas de migração** > **servidores**, selecione **avaliações** em **migrações para Azure: avaliação do servidor**.
1. Em **avaliações**, selecione uma avaliação para abri-la.

   ![Resumo da avaliação](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examinar a preparação do Azure

1. Em **preparação do Azure**, verifique se as VMs estão prontas para migração para o Azure.
1. Examine o status da VM:
    - **Pronto para o Azure**: usado quando as migrações para Azure recomendam um tamanho de VM e estimativas de custo para VMs na avaliação.
    - **Pronto com condições**: mostra problemas e correção sugerida.
    - **Não está pronto para o Azure**: mostra problemas e correção sugerida.
    - **Preparação desconhecida**: usada quando as migrações para Azure não podem avaliar a preparação devido a problemas de disponibilidade de dados.

1. Selecione um status de **preparação do Azure** . Você pode exibir os detalhes de preparação da VM. Você também pode fazer uma busca detalhada para ver os detalhes da VM, incluindo as configurações de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Examinar detalhes de custo

O resumo da avaliação mostra o custo estimado de computação e armazenamento de VMs em execução no Azure. Os custos são agregados para todas as VMs no grupo avaliado. Você pode fazer uma busca detalhada para ver os detalhes de custo de VMs específicas.

> [!NOTE]
> As estimativas de custo são baseadas nas recomendações de tamanho para um computador, seus discos e suas propriedades. As estimativas são para executar as VMs locais como VMs IaaS. A avaliação do servidor de migrações para Azure não considera os custos de PaaS ou SaaS.

Os custos de armazenamento agregados para o grupo avaliado são divididos em diferentes tipos de discos de armazenamento. 

### <a name="review-confidence-rating"></a>Rever a classificação de confiança

A avaliação de servidor de migrações para Azure atribui uma classificação de confiança a uma avaliação baseada em desempenho, de 1 estrela (mais baixa) a 5 estrelas (mais alta).

![Classificação de confiança](./media/tutorial-assess-vmware/confidence-rating.png)

A classificação de confiança ajuda a estimar a confiabilidade das recomendações de tamanho da avaliação. A classificação é baseada na disponibilidade dos pontos de dados necessários para calcular a avaliação:

**Disponibilidade do ponto de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas

[Saiba mais sobre as práticas recomendadas](best-practices-assessment.md#best-practices-for-confidence-ratings) para classificações de confiança.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configura um dispositivo de migrações para Azure. Você também criou e analisou uma avaliação.

Para saber como migrar VMs do VMware para o Azure usando a migração de servidor de migrações para Azure, continue no terceiro tutorial da série:

> [!div class="nextstepaction"]
> [Migrar VMs VMware](./tutorial-migrate-vmware.md)
