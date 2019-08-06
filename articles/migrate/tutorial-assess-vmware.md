---
title: Avaliar VMs do VMware para migração para o Azure com migrações para Azure
description: Descreve como avaliar VMs VMware locais para migração para o Azure usando as migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: hamusa
ms.openlocfilehash: 7b27637ca63ec69d7f4c33f05e7c037d67676b2d
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828298"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>Avalie as VMs do VMware com as migrações para Azure: Avaliação do Servidor

Este artigo mostra como avaliar as VMs VMware locais usando as migrações para Azure: Ferramenta de avaliação do servidor.

As migrações para [Azure](migrate-services-overview.md) fornecem um hub de ferramentas que ajudam a descobrir, avaliar e migrar aplicativos, infraestrutura e cargas de trabalho para Microsoft Azure. O Hub inclui ferramentas de migração do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros.



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

Configure um novo projeto de migrações para Azure da seguinte maneira.

1. Na portal do Azure > **todos os serviços**, procure migrações para **Azure**.
2. Em **Serviços**, selecione **migrações para Azure**.
3. Em **visão geral**, em **descobrir, avaliar e migrar servidores**, clique em **avaliar e migrar servidores**.

    ![Descobrir e avaliar servidores](./media/tutorial-assess-vmware/assess-migrate.png)

4. Em **introdução**, clique em **Adicionar ferramentas**.
5. Em **migrar projeto**, selecione sua assinatura do Azure e crie um grupo de recursos, se você não tiver um.     
6. Em **detalhes do projeto**, especifique o nome do projeto e a geografia em que você deseja criar o projeto. Há suporte para a Ásia, Europa, Reino Unido e o Estados Unidos.

    - A geografia do projeto é usada somente para armazenar os metadados coletados de VMs locais.
    - Você pode selecionar qualquer região de destino ao executar uma migração.

    ![Criar um projeto de migrações para Azure](./media/tutorial-assess-vmware/migrate-project.png)


7. Clique em **Seguinte**.
8. Em **selecionar ferramenta de avaliação**, **selecione migrações para Azure:** Avaliação > do servidor**em seguida**.

    ![Criar um projeto de migrações para Azure](./media/tutorial-assess-vmware/assessment-tool.png)

9. Em **selecionar ferramenta de migração**, selecione **ignorar a adição de uma ferramenta de migração para agora** > **em seguida**.
10. Em **examinar + adicionar ferramentas**, examine as configurações e clique em **Adicionar ferramentas**.
11. Aguarde alguns minutos para que o projeto de migrações para Azure seja implantado. Você será levado para a página do projeto. Se você não vir o projeto, poderá acessá-lo de **servidores** no painel migrações para Azure.


## <a name="set-up-the-appliance-vm"></a>Configurar a VM do dispositivo

Azure Migrate: A avaliação do servidor executa um dispositivo de VM VMware leve.

- Esse dispositivo executa a descoberta de VM e envia dados de desempenho e metadados da VM para a avaliação do servidor de migrações para Azure.
- Para configurar o dispositivo, você:
    - Baixe um arquivo de modelo OVA e importe-o para vCenter Server.
    - Crie o dispositivo e verifique se ele pode se conectar à avaliação do servidor de migrações para Azure.
    - Configure o dispositivo pela primeira vez e registre-o com o projeto de migrações para Azure.
- Você pode configurar vários dispositivos para um único projeto de migrações para Azure. Em todos os dispositivos, há suporte para a descoberta de até 35.000 VMs. Um máximo de 10.000 servidores pode ser descoberto por dispositivo.

### <a name="download-the-ova-template"></a>Baixar o modelo OVA

1. Em **objetivos** > de migração**servidores** > migraçõesparaAzure: **Avaliação**do servidor, clique em **descobrir**.
2. Em **descobrir computadores** > **são seus computadores virtualizados?** , clique em **Sim, com o hipervisor do VMware vSphere**.
3. Clique em **baixar** para baixar o. Arquivo de modelo OVA.

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
3. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://nome do*dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho do dispositivo clicando no atalho do aplicativo.
4. No aplicativo Web > **configurar os pré-requisitos**, faça o seguinte:
    - **Licença**: Aceite os termos de licenciamento e leia as informações de terceiros.
    - **Conectividade**: O aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
        - Clique em **configurações de proxy**e especifique o endereço de proxy e a porta de escuta http://ProxyIPAddress , http://ProxyFQDN no formato ou.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização de horário**: o tempo no dispositivo deve ser sincronizado com a hora da Internet para que a descoberta funcione corretamente.
    - **Instalar atualizações**: O dispositivo garante que as atualizações mais recentes sejam instaladas.
    - **Instalar o VDDK**: O dispositivo verifica se o VDDK (Kit de desenvolvimento de disco virtual) do VMWare vSphere está instalado.
        - Azure Migrate: A migração de servidor usa o VDDK para replicar máquinas durante a migração para o Azure.
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

Agora, conecte-se do dispositivo para vCenter Server e inicie a descoberta de VM.

1. Em **especificar vCenter Server detalhes**, especifique o nome (FQDN) ou o endereço IP do vCenter Server. Você pode deixar a porta padrão ou especificar uma porta personalizada na qual o vCenter Server escuta.
2. Em **nome de usuário** e **senha**, especifique as credenciais de conta somente leitura que o dispositivo usará para descobrir as VMs no servidor do vCenter. Verifique se a conta tem as [permissões necessárias para a descoberta](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Você pode fazer o escopo da descoberta limitando o acesso à conta do vCenter de acordo; Saiba mais sobre a descoberta de escopo [aqui](tutorial-assess-vmware.md#scoping-discovery).
3. Clique em **validar conexão** para garantir que o dispositivo possa se conectar ao vCenter Server.
4. Depois que a conexão for estabelecida, clique em **salvar e iniciar descoberta**.

Isso inicia a descoberta. Leva cerca de 15 minutos para que os metadados de VMs descobertas apareçam no Portal.

### <a name="scoping-discovery"></a>Descoberta de escopo

A descoberta pode ser delimitada por meio da limitação do acesso da conta do vCenter usada para descoberta. Você pode definir o escopo para vCenter Server data centers, clusters, pasta de clusters, hosts, pasta de hosts ou VMs individuais. 

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
2. No **Azure migrar-servidores** > **migrações do Azure: Página avaliação** do servidor, clique no ícone que exibe a contagem de **servidores**descobertos.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Há dois tipos de avaliações que você pode criar usando as migrações para Azure: Avaliação do Servidor.

**Locação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado em desempenho** | Avaliações com base nos dados de desempenho coletados | **Tamanho de VM recomendado**: Com base nos dados de utilização de CPU e memória.<br/><br/> **Tipo de disco recomendado (disco gerenciado Standard ou Premium)** : Com base na IOPS e na taxa de transferência dos discos locais.
**Como local** | Avaliações com base no dimensionamento local. | **Tamanho de VM recomendado**: Com base no tamanho da VM local<br/><br> **Tipo de disco recomendado**: Com base na configuração de tipo de armazenamento que você selecionar para a avaliação.


### <a name="run-an-assessment"></a>Executar uma avaliação

Execute uma avaliação da seguinte maneira:

1. Examine as [práticas recomendadas](best-practices-assessment.md) para a criação de avaliações.
2. Na guia **servidores** , em **migrações para Azure: Bloco avaliação** do servidor, clique em **avaliar**.

    ![Avaliar](./media/tutorial-assess-vmware/assess.png)

2. Em **avaliar servidores**, especifique um nome para a avaliação.
3. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades de avaliação](./media/tutorial-assess-vmware/view-all.png)

3. Em **selecionar ou criar um grupo**, selecione **criar novo**e especifique um nome de grupo. Um grupo reúne uma ou mais VMs juntas para avaliação.
4. Em **Adicionar computadores ao grupo**, selecione VMs para adicionar ao grupo.
5. Clique em **criar avaliação** para criar o grupo e executar a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-vmware/assessment-create.png)

6. Após a criação da avaliação, exiba-a nos **servidores** > **migrações para Azure: **Avaliações**de**avaliação > do servidor.
7. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.



## <a name="review-an-assessment"></a>Examinar uma avaliação

Uma avaliação descreve:

- **Preparação do Azure**: Se as VMs são adequadas para a migração para o Azure.
- **Estimativa de custo mensal**: A computação mensal estimada e os custos de armazenamento para executar as VMs no Azure.
- **Estimativa de custo de armazenamento mensal**: Custos estimados para o armazenamento em disco após a migração.

### <a name="view-an-assessment"></a>Exibir uma avaliação

1. Em **metas** >  de migração**servidores**, clique em avaliações **em migrações para Azure: Avaliação**do servidor.
2. Em **avaliações**, clique em uma avaliação para abri-la.

    ![Resumo de avaliação](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Examinar a preparação do Azure

1. Em **preparação do Azure**, verifique se as VMs estão prontas para migração para o Azure.
2. Examine o status da VM:
    - **Pronto para o Azure**: As migrações para Azure recomendam um tamanho de VM e estimativas de custo para VMs na avaliação.
    - **Pronto com condições**: Mostra problemas e correção sugerida.
    - **Não está pronto para o Azure**: Mostra problemas e correção sugerida.
    - **Preparação desconhecida**: Usado quando as migrações para Azure não podem avaliar a preparação, devido a problemas de disponibilidade de dados.

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
