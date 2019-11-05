---
title: Avaliar VMs do VMware para migração para o Azure com migrações para Azure
description: Descreve como avaliar VMs VMware locais para migração para o Azure usando as migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: hamusa
ms.openlocfilehash: 46bf756a729441bd3bc4b2b00aaa2c79fa06c0b8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521240"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>Avaliar VMs VMware com migrações para Azure: avaliação do servidor

Este artigo mostra como avaliar as VMs VMware locais, usando a ferramenta migrações para Azure: Server Assessment.

As [migrações para Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam a descobrir, avaliar e migrar aplicativos, infraestrutura e cargas de trabalho para Microsoft Azure. O Hub inclui ferramentas de migração do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros.



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

- [Conclua](tutorial-prepare-vmware.md) o primeiro tutorial desta série. Caso contrário, as instruções neste tutorial não funcionarão.
- Veja o que você deve ter feito no primeiro tutorial:
    - [Configure as permissões do Azure](tutorial-prepare-vmware.md#prepare-azure) para migrações para Azure.
    - [Prepare o VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) para avaliação. As configurações do VMware devem ser verificadas e você deve ter permissões para criar uma VM do VMware com um modelo OVA. Você também deve ter uma conta configurada para descoberta de VM. As portas necessárias devem estar disponíveis e você deve estar ciente das URLs necessárias para o acesso ao Azure.


## <a name="set-up-an-azure-migrate-project"></a>Configurar um projeto de migrações para Azure

Configure um novo projeto do Azure Migrate da seguinte forma.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Na **Descrição geral**, em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/tutorial-assess-vmware/assess-migrate.png)

4. Em **Introdução**, clique em **Adicionar ferramentas**.
5. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.     
6. Em **detalhes do projeto**, especifique o nome do projeto e a geografia em que você deseja criar o projeto. Há suporte para a Ásia, Europa, Reino Unido e o Estados Unidos.

    - A geografia do projeto só é utilizada para armazenar os metadados recolhidos das VMs no local.
    - Pode selecionar qualquer região de destino ao executar uma migração.

    ![Criar um projeto de migrações para Azure](./media/tutorial-assess-vmware/migrate-project.png)


7. Clique em **Seguinte**.
8. Em **selecionar ferramenta de avaliação**, selecione **migrações para Azure: avaliação do servidor** > **Avançar**.

    ![Criar um projeto de migrações para Azure](./media/tutorial-assess-vmware/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
10. Em **Analisar + adicionar ferramentas**, analise as definições e clique em **Adicionar ferramentas**.
11. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Será encaminhado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.


## <a name="set-up-the-appliance-vm"></a>Configurar a VM do dispositivo

Migrações para Azure: a avaliação do servidor executa um dispositivo leve de VM VMware.

- Esse dispositivo executa a descoberta de VM e envia dados de desempenho e metadados da VM para a avaliação do servidor de migrações para Azure.
- Para configurar o dispositivo, você:
    - Baixe um arquivo de modelo OVA e importe-o para vCenter Server.
    - Crie o dispositivo e verifique se ele pode se conectar à avaliação do servidor de migrações para Azure.
    - Configure o dispositivo pela primeira vez e registre-o com o projeto de migrações para Azure.
- Você pode configurar vários dispositivos para um único projeto de migrações para Azure. Em todos os dispositivos, há suporte para a descoberta de até 35.000 VMs. Um máximo de 10.000 servidores pode ser descoberto por dispositivo.

### <a name="download-the-ova-template"></a>Baixar o modelo OVA

1. Em **metas de migração** > **servidores** > **migrações para Azure: avaliação do servidor**, clique em **descobrir**.
2. Nos **Descobrir computadores** > **Os computadores estão virtualizados?** , clique **Sim, com o hipervisor VMware vSphere**.
3. Clique em **Transferir** para transferir o ficheiro de modelo .OVA.

    ![Transferir o ficheiro .ova](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>Verificar segurança

Verifique se o arquivo OVA é seguro, antes de implantá-lo.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Execute o comando abaixo para gerar o hash para o OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Para a versão 2.19.07.30, o hash gerado deve corresponder a esses valores.

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 27230f3b012187860281b912ee661709
  SHA256 | c0a5b5998b7f38ac6e57ea9a808ecc4295795e18f9ca99c367585068883f06e7


### <a name="create-the-appliance-vm"></a>Criar a VM do dispositivo

Importe o arquivo baixado e crie uma VM.

1. Na consola do vSphere Client, clique em **Ficheiro** > **Implementar Modelo OVF**.

    ![Implementar OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. No Assistente para implantar modelo de OVF > **origem**, especifique o local do arquivo ova.
3. Em **nome** e **local**, especifique um nome amigável para a VM. Selecione o objeto de inventário no qual a VM será hospedada.
5. Em **host/cluster**, especifique o host ou cluster no qual a VM será executada.
6. Em **armazenamento**, especifique o destino de armazenamento para a VM.
7. Em **Formato do Disco**, especifique o tipo e o tamanho do disco.
8. Em **mapeamento de rede**, especifique a rede à qual a VM será conectada. A rede precisa de conectividade com a Internet para enviar metadados para a avaliação do servidor de migrações para Azure.
9. Reveja e confirme as definições e, em seguida, clique em **Concluir**.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às [URLs do Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


### <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo usando as etapas a seguir.

1. Na consola do vSphere Client, clique com o botão direito do rato na VM > **Abrir Consola**.
2. Forneça o idioma, o fuso horário e a senha para o dispositivo.
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo clicando no atalho do aplicativo.
4. No aplicativo Web > **configurar os pré-requisitos**, faça o seguinte:
    - **Licença**: aceite os termos de licença e leia as informações de terceiros.
    - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
        - Clique em **configurações de proxy**e especifique o endereço de proxy e a porta de escuta, no formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização de horário**: o tempo no dispositivo deve ser sincronizado com a hora da Internet para que a descoberta funcione corretamente.
    - **Instalar atualizações**: o dispositivo garante que as atualizações mais recentes sejam instaladas.
    - **Instalar o VDDK**: o dispositivo verifica se o VDDK (Kit de desenvolvimento de disco virtual) do VMware vSphere está instalado.
        - Migrações para Azure: a migração do servidor usa o VDDK para replicar máquinas durante a migração para o Azure.
        - Baixe o VDDK 6,7 do VMware e extraia o conteúdo do zip baixado para o local especificado no dispositivo.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo com as migrações para Azure

1. Clique em **fazer logon**. Se não aparecer, verifique se você desabilitou o bloqueador de pop-ups no navegador.
2. Na nova guia, entre usando suas credenciais do Azure.
    - Entre com seu nome de usuário e senha.
    - Não há suporte para entrar com um PIN.
3. Depois de entrar com êxito, volte para o aplicativo Web.
2. Selecione a assinatura na qual o projeto de migração do Azure foi criado e, em seguida, selecione o projeto.
3. Especifique um nome para o dispositivo. O nome deve ser alfanumérico com 14 caracteres ou menos.
4. Clique em **registrar**.

## <a name="start-continuous-discovery"></a>Iniciar descoberta contínua

O dispositivo precisa se conectar ao vCenter Server para descobrir a configuração e os dados de desempenho das VMs.

### <a name="specify-vcenter-server-details"></a>Especificar detalhes do vCenter Server
1. Em **especificar vCenter Server detalhes**, especifique o nome (FQDN) ou o endereço IP do vCenter Server. Você pode deixar a porta padrão ou especificar uma porta personalizada na qual o vCenter Server escuta.
2. Em **nome de usuário** e **senha**, especifique as credenciais de conta somente leitura que o dispositivo usará para descobrir as VMs no servidor do vCenter. Verifique se a conta tem as [permissões necessárias para a descoberta](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Você pode fazer o escopo da descoberta limitando o acesso à conta do vCenter de acordo; Saiba mais sobre a descoberta de escopo [aqui](tutorial-assess-vmware.md#scoping-discovery).
3. Clique em **validar conexão** para garantir que o dispositivo possa se conectar ao vCenter Server.

### <a name="specify-vm-credentials"></a>Especificar credenciais de VM
Para a descoberta de aplicativos, funções e recursos e visualização de dependências das VMs, você pode fornecer uma credencial de VM que tenha acesso às VMs VMware. Você pode adicionar uma credencial para VMs do Windows e uma credencial para VMs do Linux. [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) sobre os privilégios de acesso necessários.

> [!NOTE]
> Essa entrada é opcional e é necessária para habilitar a descoberta de aplicativos e a visualização de dependência sem agente.

1. Em **descobrir aplicativos e dependências em VMs**, clique em **Adicionar credenciais**.
2. Selecione o **sistema operacional**.
3. Forneça um nome amigável para a credencial.
4. Em **nome de usuário** e **senha**, especifique uma conta que tenha pelo menos acesso de convidado nas VMs.
5. Clique em **Adicionar**.

Depois de especificar o vCenter Server e as credenciais da VM (opcional), clique em **salvar e inicie a descoberta** para iniciar a descoberta do ambiente local.

Leva cerca de 15 minutos para que os metadados de VMs descobertas apareçam no Portal. A descoberta de aplicativos, funções e recursos instalados leva algum tempo, a duração depende do número de VMs que estão sendo descobertas. Para as VMs 500, leva aproximadamente 1 hora para o inventário de aplicativos aparecer no portal de migrações para Azure.

### <a name="scoping-discovery"></a>Descoberta de escopo

A descoberta pode ser delimitada por meio da limitação do acesso da conta do vCenter usada para descoberta. Você pode definir o escopo para vCenter Server data centers, clusters, pasta de clusters, hosts, pasta de hosts ou VMs individuais.

Para definir o escopo, você precisa executar as seguintes etapas:
1.  Crie uma conta de usuário do vCenter.
2.  Defina uma nova função com os privilégios necessários. (<em>necessário para a migração de servidor sem agente</em>)
3.  Atribua permissões à conta de usuário em objetos do vCenter.

**Criar uma conta de usuário do vCenter**
1.  Faça logon no cliente Web vSphere como o administrador do vCenter Server.
2.  Clique em **administração** > **usuários e grupos do SSO** > guia **usuários** .
3.  Clique no ícone **novo usuário** .
4.  Preencha as informações necessárias para criar um novo usuário e clique em **OK**.

**Definir uma nova função com os privilégios necessários** (<em>necessário para a migração de servidor sem agente</em>)
1.  Faça logon no cliente Web vSphere como o administrador do vCenter Server.
2.  Navegue até **administração** > **Gerenciador de funções**.
3.  Selecione seu vCenter Server no menu suspenso.
4.  Clique em **criar** ação de função.
5.  Digite um nome para a nova função. (como <em>Azure_Migrate</em>).
6.  Atribua essas [permissões](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) à função definida recentemente.
7.  Clique em **OK**.

**Atribuir permissões em objetos do vCenter**

Há duas abordagens para atribuir permissões em objetos de inventário no vCenter à conta de usuário do vCenter com uma função atribuída a ele.
- Para avaliação do servidor, a função **somente leitura** deve ser aplicada à conta de usuário do vCenter para todos os objetos pai em que as VMs a serem descobertas estão hospedadas. Todos os objetos pai-host, pasta de hosts, cluster, pasta de clusters na hierarquia até os data center devem ser incluídos. Essas permissões devem ser propagadas para objetos filho na hierarquia.

    Da mesma forma, para a migração de servidor, uma função definida pelo usuário (pode ser chamada de <em>_Migrate do Azure</em>) com esses [privilégios](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-vcenter-server-permissions) atribuídos deve ser aplicada à conta de usuário do vCenter para todos os objetos pai onde as VMs a serem migradas estão hospedadas.

![Atribuir permissões](./media/tutorial-assess-vmware/assign-perms.png)

- A abordagem alternativa é atribuir a conta de usuário e a função no nível do datacenter e propagá-las aos objetos filho. Em seguida, dê à conta uma função **sem acesso** para cada objeto (como VMS) que você não deseja descobrir/migrar. Essa configuração é complicada. Ele expõe controles de acesso acidentais, pois cada novo objeto filho também recebe automaticamente o acesso herdado do pai. Portanto, recomendamos que você use a primeira abordagem.

> [!NOTE]
> Hoje, a avaliação do servidor não será capaz de descobrir as VMs se a conta do vCenter tiver acesso concedido no nível da pasta da VM do vCenter. Se você pretende fazer o escopo de sua descoberta por pastas de VM, você pode fazê-lo, garantindo que a conta do vCenter tenha acesso somente leitura atribuído em um nível de VM.  Veja a seguir as instruções sobre como você pode fazer isso:
>
> 1. Atribua permissões somente leitura em todas as VMs nas pastas de VM nas quais você deseja definir o escopo da descoberta.
> 2. Conceda acesso somente leitura a todos os objetos pai onde as VMs estão hospedadas. Todos os objetos pai-host, pasta de hosts, cluster, pasta de clusters-na hierarquia até os data center devem ser incluídos. Você não precisa propagar as permissões para todos os objetos filho.
> 3. Use as credenciais para descoberta selecionando datacenter como *escopo da coleção*. A configuração do RBAC garante que o usuário correspondente do vCenter terá acesso somente a VMs específicas de locatário.
>
> Observe que a pasta de hosts e clusters tem suporte.

### <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Após a descoberta, você pode verificar se as VMs aparecem no portal do Azure.

1. Abra o painel migrações para Azure.
2. Na página **migrações para Azure – servidores** > **migrações para Azure: avaliação do servidor** , clique no ícone que exibe a contagem de **servidores descobertos**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Há dois tipos de avaliações que você pode criar usando as migrações para Azure: avaliação do servidor.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado em desempenho** | Avaliações com base nos dados de desempenho coletados | **Tamanho de VM recomendado**: com base nos dados de utilização de CPU e memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado Standard ou Premium)** : com base na IOPS e na taxa de transferência dos discos locais.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de VM recomendado**: com base no tamanho da VM local<br/><br> **Tipo de disco recomendado**: com base na configuração de tipo de armazenamento que você selecionar para a avaliação.


### <a name="run-an-assessment"></a>Executar uma avaliação

Execute uma avaliação da seguinte maneira:

1. Examine as [práticas recomendadas](best-practices-assessment.md) para a criação de avaliações.
2. Na guia **servidores** , no bloco **migrações para Azure: avaliação do servidor** , clique em **avaliar**.

    ![Avaliar](./media/tutorial-assess-vmware/assess.png)

2. Em **avaliar servidores**, especifique um nome para a avaliação.
3. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades da avaliação](./media/tutorial-assess-vmware/view-all.png)

3. Em **selecionar ou criar um grupo**, selecione **criar novo**e especifique um nome de grupo. Um grupo reúne uma ou mais VMs juntas para avaliação.
4. Em **Adicionar computadores ao grupo**, selecione VMs para adicionar ao grupo.
5. Clique em **criar avaliação** para criar o grupo e executar a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-vmware/assessment-create.png)

6. Após a criação da avaliação, exiba-a em **servidores** > **migrações para Azure:** **avaliações de > de**avaliação do servidor.
7. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.



## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação descreve:

- **Preparação do Azure**: se as VMs são adequadas para a migração para o Azure.
- **Estimativa de custo mensal**: a computação mensal estimada e os custos de armazenamento para executar as VMs no Azure.
- **Estimativa de custo de armazenamento mensal**: custos estimados para armazenamento em disco após a migração.

### <a name="view-an-assessment"></a>Exibir uma avaliação

1. Em **metas de migração** >  **servidores**, clique em **avaliações** em **migrações para Azure: avaliação do servidor**.
2. Em **avaliações**, clique em uma avaliação para abri-la.

    ![Resumo da avaliação](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examinar a preparação do Azure

1. Em **preparação do Azure**, verifique se as VMs estão prontas para migração para o Azure.
2. Examine o status da VM:
    - **Pronto para o Azure**: as migrações para Azure recomendam um tamanho de VM e estimativas de custo para VMs na avaliação.
    - **Pronto com condições**: mostra problemas e correção sugerida.
    - **Não está pronto para o Azure**: mostra problemas e correção sugerida.
    - **Preparação desconhecida**: usada quando as migrações para Azure não podem avaliar a preparação, devido a problemas de disponibilidade de dados.

2. Clique em um status de **preparação do Azure** . Você pode exibir os detalhes de preparação da VM e fazer uma busca detalhada para ver os detalhes da VM, incluindo as configurações de computação, armazenamento e rede.



### <a name="review-cost-details"></a>Examinar detalhes de custo

Essa exibição mostra o custo estimado de computação e armazenamento de VMs em execução no Azure.

1. Examine os custos mensais de computação e armazenamento. Os custos são agregados para todas as VMs no grupo avaliado.

    - As estimativas de custo são baseadas nas recomendações de tamanho para um computador e seus discos e propriedades.
    - Os custos mensais estimados para computação e armazenamento são mostrados.
    - A estimativa de custo é para executar as VMs locais como VMs de IaaS. A avaliação do servidor de migrações para Azure não considera os custos de PaaS ou SaaS.

2. Você pode examinar as estimativas de custo de armazenamento mensal. Essa exibição mostra os custos de armazenamento agregados para o grupo avaliado, divididos em diferentes tipos de discos de armazenamento.
3. Você pode fazer uma busca detalhada para ver os detalhes de VMs específicas.


### <a name="review-confidence-rating"></a>Rever a classificação de confiança

Quando você executa avaliações baseadas em desempenho, uma classificação de confiança é atribuída à avaliação.

![Classificação de confiança](./media/tutorial-assess-vmware/confidence-rating.png)

- Uma classificação de 1 estrela (mais baixa) a 5 estrelas (mais alta) é concedida.
- A classificação de confiança ajuda a estimar a confiabilidade das recomendações de tamanho fornecidas pela avaliação.
- A classificação de confiança baseia-se na disponibilidade dos pontos de dados necessários para calcular a avaliação.

As classificações de confiança para uma avaliação são as seguintes.

**Disponibilidade do ponto de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 Estrela
21%-40% | 2 Estrelas
41%-60% | 3 Estrelas
61%-80% | 4 Estrelas
81%-100% | 5 Estrelas

[Saiba mais](best-practices-assessment.md#best-practices-for-confidence-ratings) sobre as práticas recomendadas para classificações de confiança.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Configurar um dispositivo de migrações para Azure
> * Criado e revisado uma avaliação

Continue no terceiro tutorial da série para saber como migrar VMs VMware para o Azure com a migração de servidor migrações para Azure.

> [!div class="nextstepaction"]
> [Migrar VMs VMware](./tutorial-migrate-vmware.md)
