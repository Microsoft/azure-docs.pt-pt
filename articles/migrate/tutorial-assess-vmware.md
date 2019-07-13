---
title: Avaliar VMware VMs para migração para o Azure com o Azure Migrate
description: Descreve como avaliar VMs de VMware no local para migração para o Azure com o Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: hamusa
ms.openlocfilehash: 5f70037b1e6ce284b55ff5ff0ae38eb50c320122
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868662"
---
# <a name="assess-vmware-vms-with-azure-migrate-server-assessment"></a>Avaliar VMs VMware com o Azure Migrate: Avaliação de servidor

Este artigo mostra-lhe como avaliar VMs de VMware no local, utilizando o Azure Migrate: Ferramenta de avaliação do servidor.

[O Azure Migrate](migrate-services-overview.md) fornece um hub de ferramentas que ajudam a detetar, avaliar e migrar cargas de trabalho, aplicações e infraestrutura para o Microsoft Azure. O hub inclui as ferramentas do Azure Migrate e ofertas de (ISV fornecedor) de terceiros independentes de software.



Este tutorial é a segunda de uma série que demonstra como avaliar e migrar VMs de VMware para o Azure. Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Configure um projeto do Azure Migrate.
> * Configure uma aplicação do Azure Migrate, o que é executado no local para avaliar VMs.
> * Inicie deteção contínua de VMs no local. A aplicação envia dados de configuração e desempenho para as VMs detetadas para o Azure.
> * Agrupar VMs detetadas e avaliar o grupo VM.
> * Reveja a avaliação.



> [!NOTE]
> Tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que pode configurar rapidamente a uma prova de conceito. Tutoriais utilizam as opções predefinidas, sempre que possível e não mostram todas as configurações possíveis e caminhos. Para obter instruções detalhadas, consulte os artigos de procedimentos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- [Completa](tutorial-prepare-vmware.md) o primeiro tutorial nesta série. Caso contrário, as instruções deste tutorial não funcionarão.
- Eis o que deveria ter feito no primeiro tutorial:
    - [Configurar as permissões do Azure](tutorial-prepare-vmware.md#prepare-azure) para o Azure Migrate.
    - [Preparar o VMware](tutorial-prepare-vmware.md#prepare-for-vmware-vm-assessment) para avaliação. Definições de VMware devem ser verificadas e deve ter permissões para criar uma VM do VMware com um modelo de OVA. Também deverá ter uma conta configurada para deteção de VMS. As portas necessárias devem estar disponíveis e deve estar ciente dos URLs necessários para acesso ao Azure.


## <a name="set-up-an-azure-migrate-project"></a>Configurar um projeto do Azure Migrate

Configure um novo projeto do Azure Migrate da seguinte forma.

1. No portal do Azure > **todos os serviços**, procure **Azure Migrate**.
2. Sob **serviços**, selecione **Azure Migrate**.
3. Na **descrição geral**, em **detetar, avaliar e migrar servidores**, clique em **avaliar e migrar servidores de**.

    ![Detetar e avaliar os servidores](./media/tutorial-assess-vmware/assess-migrate.png)

4. Na **introdução**, clique em **adicionar ferramentas**.
5. Na **migrar projeto**, selecione a sua subscrição do Azure e criar um grupo de recursos se não tiver uma.     
6. Na **detalhes do projeto**, especifique o nome do projeto e na geografia no qual pretende criar o projeto. Ásia, Europa, Reino Unido e Estados Unidos são suportadas.

    - A geografia do projeto é utilizada apenas para armazenar os metadados recolhidos das VMs no local.
    - Pode selecionar qualquer região de destino ao executar uma migração.

    ![Criar um projeto do Azure Migrate](./media/tutorial-assess-vmware/migrate-project.png)


7. Clique em **Seguinte**.
8. Na **ferramenta de avaliação Select**, selecione **Azure Migrate: Avaliação de servidor** > **próxima**.

    ![Criar um projeto do Azure Migrate](./media/tutorial-assess-vmware/assessment-tool.png)

9. Na **ferramenta de migração Select**, selecione **ignorar a adição de uma ferramenta de migração por agora** > **seguinte**.
10. Na **rever + adicionar ferramentas**, reveja as definições e clique em **adicionar ferramentas**.
11. Aguarde alguns minutos para que o projeto do Azure Migrate a implementar. Será levado à página do projeto. Se não vir o projeto, pode acessá-lo da **servidores** no dashboard do Azure Migrate.


## <a name="set-up-the-appliance-vm"></a>Configurar a aplicação da VM

Azure Migrate: Avaliação do servidor é executada uma simples aplicação de VM de VMware.

- Este dispositivo executa a deteção de VMS e envia dados de metadados e o desempenho da VM para avaliação do servidor de migrar do Azure.
- Para configurar a aplicação da:
    - Transferir um ficheiro de modelo OVA e importe-o ao vCenter Server.
    - Criar a aplicação e verifique se pode ligar a avaliação do servidor de migrar do Azure.
    - Configurar a aplicação pela primeira vez e registrá-la com o projeto do Azure Migrate.
- Pode configurar várias aplicações para um único projeto do Azure Migrate. Em todas as aplicações, a deteção de VMs até 35 000 é suportada. Um máximo de 10 000 servidores pode ser detetado por aplicação.

### <a name="download-the-ova-template"></a>Transferir o modelo de OVA

1. Na **objetivos de migração** > **servidores** > **do Azure Migrate: Avaliação de servidor**, clique em **detetar**.
2. Na **detetar máquinas** > **são as suas máquinas virtualizadas?** , clique em **Sim, com o hipervisor do VMWare vSphere**.
3. Clique em **transferir** para transferir o. Ficheiro de modelo OVA.

    ![Transferir o ficheiro .ova](./media/tutorial-assess-vmware/download-ova.png)


### <a name="verify-security"></a>Certifique-se de segurança

Verifique se o ficheiro OVA é seguro, antes de o implementar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Execute o comando abaixo para gerar o hash para o OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Para a versão 1.19.06.27, o hash gerado deve corresponder a estes valores. 

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 605d208ac5f4173383f616913441144e
  SHA256 | 447d16bd55f20f945164a1189381ef6e98475b573d6d1c694f3e5c172cfc30d4


### <a name="create-the-appliance-vm"></a>Criar a aplicação da VM

Importar o ficheiro transferido e criar uma VM.

1. Na consola do vSphere Client, clique em **Ficheiro** > **Implementar Modelo OVF**.

    ![Implementar OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. No Assistente de implementação de modelo OVF > **origem**, especifique a localização do ficheiro OVA.
3. Na **Name** e **localização**, especifique um nome amigável para a VM. Selecione o objeto de inventário em que a VM será alojada.
5. Na **anfitrião/Cluster**, especifique o anfitrião ou cluster no qual a VM será executado.
6. Na **armazenamento**, especifique o destino de armazenamento para a VM.
7. Em **Formato do Disco**, especifique o tipo e o tamanho do disco.
8. Na **mapeamento da rede**, especifique a rede à qual se ligará a VM. A rede necessita de conectividade com a internet, para enviar metadados para a avaliação do servidor de migrar do Azure.
9. Reveja e confirme as definições e, em seguida, clique em **Concluir**.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso da aplicação para o Azure

Certifique-se de que a aplicação da VM pode ligar à [URLs do Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


### <a name="configure-the-appliance"></a>Configurar a aplicação

Configure a aplicação através dos seguintes passos.

1. Na consola do vSphere Client, clique com o botão direito do rato na VM > **Abrir Consola**.
2. Fornece o idioma, fuso horário e palavra-passe para a aplicação.
3. Abra um browser em qualquer máquina que pode ligar à VM e abrir o URL da aplicação web da aplicação: **https://*nome da aplicação ou o endereço IP*: 44368**.

   Em alternativa, pode abrir a aplicação no ambiente de trabalho da aplicação ao clicar no atalho de aplicação.
4. Na aplicação web > **configurar pré-requisitos**, efetue o seguinte procedimento:
    - **Licença**: Aceite os termos de licenciamento e leia as informações de terceiros.
    - **Conectividade**: A aplicação verifica se a VM tem acesso à internet. Se a VM utiliza um proxy:
        - Clique em **definições de Proxy**e especifique o endereço de proxy e porta de escuta, o formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização de hora**: A hora da aplicação deve ser sincronizada com o tempo de internet para a deteção funcione corretamente.
    - **Instalar atualizações**: A aplicação da garante que as atualizações mais recentes estão instaladas.
    - **Instalar VDDK**: A aplicação verifica esse Kit de desenvolvimento de disco Virtual (VDDK) está instalado do VMWare vSphere.
        - Azure Migrate: Migração do servidor utiliza o VDDK para replicar máquinas durante a migração para o Azure.
        - Transferir VDDK 6.7 do VMware e extrair os conteúdos do zip transferido para a localização especificada na aplicação.

### <a name="register-the-appliance-with-azure-migrate"></a>Registre-se a aplicação com o Azure Migrate

1. Clique em **iniciar sessão**. Se não aparecer, certifique-se de que ter desabilitado o Bloqueador de pop-up no browser.
2. Na nova guia, inicie sessão com as credenciais do Azure.
    - Inicie sessão com o nome de utilizador e palavra-passe.
    - Inicie sessão com um PIN não é suportada.
3. Depois de iniciar sessão com êxito na, volte para a aplicação web.
2. Selecione a subscrição na qual foi criado o projeto do Azure Migrate, em seguida, selecione o projeto.
3. Especifique um nome para a aplicação. O nome deve ser alfanumérica com 14 caracteres ou menos.
4. Clique em **registar**.


## <a name="start-continuous-discovery"></a>Iniciar a deteção contínua

Agora, ligar a partir da aplicação ao vCenter Server e iniciar a deteção de VMS.

1. Na **especificar detalhes do vCenter Server**, especifique o nome (FQDN) ou o endereço IP do vCenter Server. Pode deixar a porta predefinida ou especificar uma porta personalizada, no qual o vCenter Server aguarda.
2. Na **nome de utilizador** e **palavra-passe**, especifique as credenciais de conta só de leitura que a aplicação irá utilizar para detetar VMs no vCenter server. Certifique-se de que a conta tem o [permissões necessárias para a deteção](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions). Pode definir o âmbito de deteção ao limitar o acesso à conta do vCenter em conformidade; Saiba mais sobre o controlo de âmbito de deteção [aqui](tutorial-assess-vmware.md#scoping-discovery).
3. Clique em **validar ligação** para se certificar de que a aplicação pode ligar ao vCenter Server.
4. Depois da ligação é estabelecida, clique em **guardar e iniciar a deteção**.

Esta ação inicia a deteção. Demora cerca de 15 minutos para os metadados de VMs detetadas a aparecer no portal.

### <a name="scoping-discovery"></a>Controlo de âmbito de deteção

Deteção pode ser confinada ao limitar o acesso da conta do vCenter utilizada para a deteção. Pode definir o âmbito para centros de dados do vCenter Server, clusters, pasta de clusters, anfitriões, pasta de anfitriões ou VMs individuais. 

> [!NOTE]
> Atualmente, a avaliação de servidor não é capaz de detetar VMs, se a conta do vCenter tem acesso concedido ao nível de pasta VM do vCenter. Se quiser definir o âmbito sua deteção por pastas de VM, pode fazê-lo ao garantir que o vCenter conta tem acesso só de leitura atribuído a um nível VM.  Seguem-se instruções sobre como pode fazer isso:
>
> 1. Atribua permissões só de leitura em todas as VMs nas pastas VM à qual pretende definir o âmbito de deteção. 
> 2. Conceder acesso só de leitura a todos os objetos de principal onde estão alojadas as VMs. Todos os objetos principais - host, pasta de anfitriões, cluster, pasta de clusters - na hierarquia até o Centro de dados estão a ser incluído. Não é necessário propagar as permissões para todos os objetos subordinados.
> 3. Utilizar as credenciais para a deteção de selecionar o datacenter como *âmbito da coleção*. O RBAC configurar garante que o utilizador correspondente do vCenter terão acesso às VMs apenas específico de inquilino.
>
> Tenha em atenção essa pasta de anfitriões e clusters são suportados.

### <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Após a deteção, pode verificar se as VMs aparecem no portal do Azure.

1. Abra o dashboard do Azure Migrate.
2. Na **do Azure Migrate - servidores** > **do Azure Migrate: Avaliação de servidor** página, clique no ícone que exibe a contagem do **detetados servidores**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Existem dois tipos de avaliações, que pode criar com o Azure Migrate: Avaliação do servidor.

**Assessment** | **Detalhes** | **Dados**
--- | --- | ---
**Com base em desempenho** | Avaliações com base nos dados de desempenho recolhidos | **Tamanho VM recomendado**: Com base nos dados de utilização de CPU e memória.<br/><br/> **Recomendado o tipo de disco (standard ou premium disco gerido)** : Com base no IOPS e débito dos discos no local.
**Como no local** | Avaliações com base no dimensionamento no local. | **Tamanho VM recomendado**: Com base no tamanho VM no local<br/><br> **Recomendado o tipo de disco**: Com base na definição de tipo de armazenamento que selecionar para a avaliação.


### <a name="run-an-assessment"></a>Executar uma avaliação

Execute uma avaliação da seguinte forma:

1. Reveja os [melhores práticas](best-practices-assessment.md) para a criação de avaliações.
2. Na **servidores** separador **Azure Migrate: Avaliação de servidor** mosaico, clique em **avaliação**.

    ![Avaliar](./media/tutorial-assess-vmware/assess.png)

2. Na **avaliar servidores**, especifique um nome para a avaliação.
3. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades de avaliação](./media/tutorial-assess-vmware/view-all.png)

3. Na **selecione ou crie um grupo**, selecione **criar nova**e especifique um nome de grupo. Um grupo de coleta uma ou mais VMs em conjunto para avaliação.
4. Na **Adicionar máquinas ao grupo**, selecione as VMs para adicionar ao grupo.
5. Clique em **Criar avaliação** para criar o grupo e execute a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-vmware/assessment-create.png)

6. Após a criação da avaliação, visualize-a em **servidores** > **Azure Migrate: Avaliação de servidor** > **avaliações**.
7. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.



## <a name="review-an-assessment"></a>Reveja uma avaliação

Descreve uma avaliação:

- **Preparação para o Azure**: Se as VMs são adequadas para migração para o Azure.
- **Estimativa de custo mensal**: As computação e armazenamento os custos mensais estimados para executar as VMs no Azure.
- **Estimativa de custos de armazenamento mensal**: Custos estimados para o armazenamento de disco após a migração.

### <a name="view-an-assessment"></a>Ver uma avaliação

1. Na **objetivos de migração** >  **servidores**, clique em **avaliações** no **Azure Migrate: Avaliação de servidor**.
2. Na **avaliações**, clique numa avaliação para abri-lo.

    ![Resumo da avaliação](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Reveja a preparação para o Azure

1. Na **preparação para o Azure**, verificar se as VMs estão prontas para a migração para o Azure.
2. Reveja o estado da VM:
    - **Preparado para o Azure**: O Azure Migrate recomenda um tamanho VM e o custo estimativas para as VMs na avaliação.
    - **Preparado com condições**: Mostra os problemas e de remediação sugeridos.
    - **Não preparado para o Azure**: Mostra os problemas e de remediação sugeridos.
    - **Preparação desconhecida**: Utilizado quando o Azure Migrate não é possível avaliar a preparação, devido a problemas de disponibilidade de dados.

2. Clique num **preparação para o Azure** estado. Pode ver os detalhes de preparação da VM e desagregar para ver detalhes VM, incluindo computação, armazenamento e as definições de rede.



### <a name="review-cost-details"></a>Detalhes da revisão de custos

Esta vista mostra o custo estimado de computação e armazenamento de VMs em execução no Azure.

1. Reveja os custos de computação e armazenamento mensais. Os custos são agregados para todas as VMs no grupo de avaliadas.

    - Estimativas de custos baseiam-se as recomendações de tamanho para uma máquina e os discos e propriedades.
    - São apresentados os custos mensais estimados para computação e armazenamento.
    - É a estimativa de custos para executar as VMs no local como IaaS VMs. Avaliação Server do Azure Migrate não considera os custos de PaaS ou SaaS.

2. Pode rever mensalmente estimativas de custo de armazenamento. Esta vista mostra os custos de armazenamento agregado para o grupo avaliado, divididas em diferentes tipos de discos de armazenamento.
3. Pode desagregar para ver os detalhes para VMs específicas.


### <a name="review-confidence-rating"></a>Rever a classificação de confiança

Quando executar avaliações baseadas em desempenho, é atribuída uma classificação de confiança para a avaliação.

![Classificação de confiança](./media/tutorial-assess-vmware/confidence-rating.png)

- Concedidos uma classificação de estrelas de 1 (menor) a 5 estrelas (mais alta).
- A classificação de confiança ajuda a calcular a fiabilidade das recomendações de tamanho fornecidas pela avaliação.
- A classificação de confiança baseia-se sobre a disponibilidade de pontos de dados necessários para calcular a avaliação.

Seguem-se as classificações de confiança para uma avaliação.

**Disponibilidade de ponto de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 Estrela
21%-40% | 2 Estrelas
41%-60% | 3 Estrelas
61%-80% | 4 Estrelas
81%-100% | 5 Estrelas

[Saiba mais](best-practices-assessment.md#best-practices-for-confidence-ratings) sobre as melhores práticas para classificações de confiança.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial:

> [!div class="checklist"]
> * Configurar uma aplicação do Azure Migrate
> * Criado e revisados uma avaliação

Avance para o terceiro tutorial da série, para saber como migrar VMs de VMware para o Azure com o Azure Migrate migração do servidor.

> [!div class="nextstepaction"]
> [Migrar VMs de VMware](./tutorial-migrate-vmware.md)
