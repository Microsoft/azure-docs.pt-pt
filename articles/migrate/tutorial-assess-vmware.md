---
title: Avaliar VMware VMs com avaliação do servidor Azure Migrate
description: Descreve como avaliar vMware VMware no local para migração para Azure usando Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: dd00f800003724b3a5c15d265a5428272e1762fb
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290218"
---
# <a name="assess-vmware-vms-with-server-assessment"></a>Avaliar VMs VMware com Avaliação do Servidor

Este artigo mostra-lhe como avaliar no local máquinas virtuais VMware (VMs), utilizando a ferramenta [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool)


Este tutorial é o segundo de uma série que demonstra como avaliar e migrar VMware VMs para Azure. Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Crie um projeto Azure Migrate.
> * Crie um aparelho Azure Migrate que funciona no local para avaliar os VM.
> * Inicie a descoberta contínua de VMs no local. O aparelho envia dados de configuração e desempenho para VMs descobertos para Azure.
> * O grupo descobriu VMs e avaliou o grupo VM.
> * Reveja a avaliação.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja os artigos de como fazer.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- [Complete o primeiro tutorial](tutorial-prepare-vmware.md) desta série. Se não o fizeres, as instruções deste tutorial não funcionam.
- Eis o que devias ter feito no primeiro tutorial:
    - [Prepare o Azure](tutorial-prepare-vmware.md#prepare-azure) para trabalhar com a Azure Migrate.
    - [Preparar vMware para avaliação](tutorial-prepare-vmware.md#prepare-for-assessment) para avaliação. Isto inclui verificar as definições de VMware, criar uma conta que o Azure Migrate pode usar para aceder ao servidor vCenter.
    - [Verifique](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment) o que precisa para implantar o aparelho Azure Migrate para avaliação de VMware.

## <a name="set-up-an-azure-migrate-project"></a>Criar um projeto Azure Migrate

Crie um novo projeto Azure Migrate da seguinte forma:

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Em **Visão Geral**, em **Discover, avaliar e migrar servidores,** selecione **Avaliar e migrar servidores**.

   ![Botão para avaliar e migrar servidores](./media/tutorial-assess-vmware/assess-migrate.png)

4. Em **Iniciar,** **selecione Adicionar ferramentas**.
5. Em **Migrar projeto**, selecione a sua subscrição do Azure e crie um grupo de recursos, caso não tenha um.     
6. Em **Detalhes do Projeto,** especifique o nome do projeto e a geografia em que pretende criar o projeto. Reveja geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

   ![Caixas para nome e região do projeto](./media/tutorial-assess-vmware/migrate-project.png)

7. Selecione **Seguinte**.
8. Na **ferramenta de avaliação Select**, selecione **Azure Migrate: Avaliação do servidor**Em  >  **seguida**.

   ![Seleção para a ferramenta de avaliação do servidor](./media/tutorial-assess-vmware/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
10. Em **Rever + adicionar ferramentas,** rever as definições e selecionar **Ferramentas Adicionar**.
11. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Será encaminhado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.

## <a name="set-up-the-azure-migrate-appliance"></a>Configurar o aparelho Azure Migrate

Azure Migrate:A Avaliação do Servidor utiliza um aparelho Azure Migrate leve. O aparelho realiza a descoberta de VM e envia metadados VM e dados de desempenho para a Azure Migrate. O aparelho pode ser montado de várias maneiras.

- Configurar um VMware VM usando um modelo OVA descarregado. Este é o método usado neste tutorial.
- Configurar num VMware VM ou numa máquina física com um script instalador PowerShell. [Este método](deploy-appliance-script.md) deve ser utilizado se não puder configurar um VM usando um modelo OVA, ou se estiver no Governo Azure.

Depois de criar o aparelho, verifique se pode ligar-se ao Azure Migrate:Server Assessment, configurá-lo pela primeira vez e registá-lo com o projeto Azure Migrate.


### <a name="download-the-ova-template"></a>Descarregue o modelo OVA

1. In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, select **Discover**.
2. In **Discover machines**  >  **Are your machines virtualized?** **Yes, with VMware vSphere hypervisor**
3. Selecione **Baixar** para descarregar o ficheiro do modelo OVA.

   ![Seleções para descarregar um ficheiro OVA](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Verificar segurança

Verifique se o ficheiro OVA está seguro, antes de o implementar:

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Verifique as versões mais recentes do aparelho e os valores do haxixe:

    - Para a nuvem pública de Azure:
    
        **Algoritmo** | **Transferência** | **SHA256**
        --- | --- | ---
        VMware (10,9 GB) | [Versão mais recente](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6b6ce7402b79f234bc0fe69663d

    - Para o Governo de Azure:
    
        **Algoritmo** | **Transferência** | **SHA256**
        --- | --- | ---
        VMware (63.1 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d582038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de


### <a name="create-the-appliance-vm"></a>Criar o aparelho VM

Importe o ficheiro descarregado e crie um VM:

1. Na consola vSphere Client, selecione **File**  >  **OVF de implementação de ficheiros**.

   ![Comando do menu para implantar um modelo OVF](./media/tutorial-assess-vmware/deploy-ovf.png)

2. No Assistente de Modelo de OVF de implementação > **Fonte**, especifique a localização do ficheiro OVA.
3. No **Nome** e **Localização**, especifique um nome amigável para o VM. Selecione o objeto de inventário no qual o VM será hospedado.
4. No **Host/Cluster**, especifique o hospedeiro ou o cluster em que o VM irá funcionar.
5. No **Armazenamento**, especifique o destino de armazenamento para o VM.
6. Em **Formato do Disco**, especifique o tipo e o tamanho do disco.
7. No **Mapeamento de Rede,** especifique a rede à qual o VM se ligará. A rede necessita de conectividade com a Internet para enviar metadados para a Avaliação do Servidor Migratório Azure.
8. Reveja e confirme as definições e, em seguida, **selecione Terminar**.

## <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o aparelho VM pode ligar-se aos URLs Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)

### <a name="configure-the-appliance"></a>Configure o aparelho

Coloque o aparelho pela primeira vez.

> [!NOTE]
> Se configurar o aparelho utilizando um [script PowerShell](deploy-appliance-script.md) em vez do OVA descarregado, os dois primeiros passos deste procedimento não são relevantes.

1. Na consola vSphere Client, clique com o botão direito no VM e, em seguida, selecione **Open Console**.
2. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
3. Abra um browser em qualquer máquina que possa ligar ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho selecionando o atalho da aplicação.
1. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
   - **Licença**: Aceite os termos da licença e leia as informações de terceiros.
   - **Conectividade**: A aplicação verifica se o VM tem acesso à Internet. Se o VM utilizar um representante:
     - **Selecione as definições de Procuração**e especifique o endereço de procuração e a porta de audição no formulário http://ProxyIPAddress ou http://ProxyFQDN .
     - Especifique as credenciais se o proxy precisar de autenticação.
     - Apenas é suportado o proxy HTTP.
   - **Sincronização de tempo**: O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta funcione corretamente.
   - **Instalação de atualizações**: O aparelho assegura a instalação das atualizações mais recentes.
   - **Instalação VDDK**: O aparelho verifica se o Kit de Desenvolvimento de Discos Virtuais VMware vSphere (VDDK) está instalado. Se não estiver instalado, baixe o VDDK 6.7 da VMware e extraia o conteúdo zip descarregado para a localização especificada no aparelho.

     A migração do servidor Azure Migrate usa o VDDK para replicar máquinas durante a migração para Azure.       

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Selecione **Iniciar sessão**. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.
2. No novo separador, inscreva-se utilizando o seu nome de utilizador E palavra-passe Azure.
   
   O s-in com um PIN não é suportado.
3. Depois de iniciar sins com sucesso, volte para a aplicação web.
4. Selecione a subscrição em que o projeto Azure Migrate foi criado e, em seguida, selecione o projeto.
5. Especifique um nome para o aparelho. O nome deve ser alfanumérico com 14 caracteres ou menos.
6. Selecione **Registar**.


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

O aparelho precisa de se ligar ao servidor vCenter para descobrir a configuração e os dados de desempenho dos VMs.

### <a name="specify-vcenter-server-details"></a>Especificar detalhes do vCenter Server
1. Nos **detalhes do Servidor do VCenter,** especifique o nome (FQDN) ou endereço IP da instância vCenter Server. Pode deixar a porta predefinida ou especificar uma porta personalizada na qual o vCenter Server ouve.
2. No **nome de utilizador** e na **palavra-passe**, especifique as credenciais de conta do servidor vCenter que o aparelho utilizará para descobrir VMs na instância do servidor vCenter. 

    - Deveria ter criado uma conta com as permissões necessárias no [tutorial anterior.](tutorial-prepare-vmware.md#set-up-permissions-for-assessment)
    - Se pretender estender a descoberta a objetos VMware específicos (centros de dados vCenter Server, clusters, uma pasta de clusters, anfitriões, uma pasta de anfitriões ou VMs individuais.), reveja as instruções [deste artigo](set-discovery-scope.md) para restringir a conta utilizada pela Azure Migrate.

3. **Selecione Validar** a ligação para se certificar de que o aparelho pode ligar-se ao servidor vCenter.
4. Nas **aplicações e dependências de Discover em VMs,** clique opcionalmente em **Adicionar credenciais,** e especifique o sistema operativo para o qual as credenciais são relevantes, e o nome de utilizador e senha de acesso das credenciais. Em seguida, clique em **Adicionar**.

    - Você opcionalmente adiciona credenciais aqui se você criou uma conta para usar para o recurso de descoberta de [aplicações](how-to-discover-applications.md), ou a [funcionalidade de análise de dependência sem agente](how-to-create-group-machine-dependencies-agentless.md).
    - Se não estiver a utilizar estas funcionalidades, pode saltar esta definição.
    - Reveja as credenciais necessárias para [a descoberta de apps,](migrate-support-matrix-vmware.md#application-discovery-requirements)ou para [análise sem agente.](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)

5. **Salve e comece a descoberta,** para iniciar a descoberta de VM.

A descoberta funciona da seguinte forma:
- Leva cerca de 15 minutos para que os metadados VM descobertos apareçam no portal.
- A descoberta de aplicações, funções e funcionalidades instaladas leva algum tempo. A duração depende do número de VMs descobertos. Para 500 VMs, leva aproximadamente uma hora para o inventário da aplicação aparecer no portal Azure Migrate.

### <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Após a descoberta, pode verificar se os VMs aparecem no portal Azure:

1. Abra o painel Azure Migrate.
2. Em **Azure Migrate - Servidores**  >  **Azure Migrate: Avaliação**do servidor , selecione o ícone que exibe a contagem para **servidores descobertos**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Pode criar dois tipos de avaliações utilizando a Avaliação do Servidor Azure Migrate:

**Tipo de Avaliação** | **Detalhes**
--- | --- 
**VM do Azure** | Avaliações para migrar os seus servidores no local para máquinas virtuais Azure. <br/><br/> Pode avaliar os seus [VMS VMware](how-to-set-up-appliance-vmware.md)no local, [VMs hiper-V](how-to-set-up-appliance-hyper-v.md)e [servidores físicos](how-to-set-up-appliance-physical.md) para migração para Azure utilizando este tipo de avaliação. [Saiba mais](concepts-assessment-calculation.md)
**Solução VMware no Azure (AVS)** | Avaliações para migrar os seus servidores no local para [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Pode avaliar os seus [VMS VMware](how-to-set-up-appliance-vmware.md) no local para migração para Azure VMware Solution (AVS) utilizando este tipo de avaliação. [Saiba mais](concepts-azure-vmware-solution-assessment-calculation.md)

A Avaliação do Servidor fornece duas opções de critérios de dimensionamento:

**Critérios de dimensionamento** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações que fazem recomendações com base em dados de desempenho recolhidos | **Avaliação Azure VM**: A recomendação do tamanho de VM baseia-se em dados de cpu e utilização da memória.<br/><br/> A recomendação do tipo de disco (hdd/SSD padrão ou discos geridos por prémios) baseia-se no IOPS e na produção dos discos no local.<br/><br/> **Avaliação da Solução Azure VMware (AVS):** A recomendação dos nós AVS baseia-se em dados de CPU e utilização da memória.
**As-is in-ins** | Avaliações que não usam dados de desempenho para fazer recomendações. | **Avaliação Azure VM**: A recomendação do tamanho de VM baseia-se no tamanho VM no local<br/><br> O tipo de disco recomendado baseia-se no que seleciona na definição do tipo de armazenamento para a avaliação.<br/><br/> **Avaliação da Solução Azure VMware (AVS):** A recomendação dos nós AVS baseia-se no tamanho VM no local.

## <a name="run-an-assessment"></a>Executar uma avaliação

Executar uma *avaliação Azure VM* da seguinte forma:

1. Reveja as [melhores práticas](best-practices-assessment.md) para criar avaliações.
2. No separador **Servidores,** no **Azure Migrate: Telha de Avaliação** do Servidor, selecione **Avaliar**.

   ![Localização do botão Avaliar](./media/tutorial-assess-vmware/assess.png)

3. Nos **servidores avaliação**, selecione o tipo de avaliação como "Azure VM", selecione a fonte de descoberta e especifique o nome de avaliação.

    ![Avaliação Básicos](./media/tutorial-assess-vmware/assess-servers-azurevm.png)
 
4. Selecione **Ver tudo**e, em seguida, reveja as propriedades de avaliação.

   ![Propriedades de avaliação](./media/tutorial-assess-vmware/view-all.png)

5. Clique **ao lado** de **Selecionar máquinas para avaliar**. Em **Select ou criar um grupo**, selecione Create **New**, e especifique um nome de grupo. Um grupo reúne um ou mais VMs para avaliação.
6. No **Adicionar máquinas ao grupo,** selecione VMs para adicionar ao grupo.
7. Clique **ao lado** do Review + crie **avaliação** para rever os detalhes da avaliação.
8. Selecione **Create Assessment** para criar o grupo e executar a avaliação.

   ![Avaliar servidores](./media/tutorial-assess-vmware/assessment-create.png)

8. Após a avaliação ser criada, veja-a nos **Servidores**  >  **Azure Migrate: Avaliações de Avaliação do Servidor**  >  **Assessments**.
9. Selecione **a avaliação de exportação** para descarregá-lo como um ficheiro Excel.

Se pretender fazer uma **avaliação da Solução VMware (AVS) do Azure VMware,** siga os passos [aqui](how-to-create-azure-vmware-solution-assessment.md)mencionados.


## <a name="review-an-assessment"></a>Rever uma avaliação

Uma avaliação descreve:

- **Prontidão azul**: Se os VM são adequados para a migração para Azure.
- **Estimativa mensal dos custos**: Os custos estimados mensalmente de cálculo e armazenamento para a execução dos VMs em Azure.
- **Estimativa mensal dos custos de armazenamento**: Custos estimados para o armazenamento do disco após a migração.

Para visualizar uma avaliação:

1. Nos **objetivos de migração**  >  **Servidores**, selecione **Avaliações** em **Azure Migrate: Avaliação do servidor**.
2. Em **Avaliações,** selecione uma avaliação para abri-la.

   ![Resumo da avaliação](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Rever prontidão Azure

1. Em **Azure prontidão**, verifique se os VMs estão prontos para a migração para Azure.
2. Reveja o estado de VM:
    - **Pronto para Azure**: Usado quando a Azure Migrate recomenda uma estimativa de tamanho VM e custos para os VMs na avaliação.
    - **Pronto com condições**: Apresenta problemas e sugeriu remediação.
    - **Não está pronto para o Azure**: Apresenta problemas e sugeriu a reparação.
    - **Prontidão desconhecida**: Usado quando a Azure Migrate não consegue avaliar a prontidão devido a problemas de disponibilidade de dados.

3. Selecione um estado **de prontidão Azure.** Pode ver detalhes de prontidão em VM. Também pode perfurar para ver detalhes do VM, incluindo configurações de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Rever detalhes de custos

O resumo da avaliação mostra o custo estimado do cálculo e armazenamento dos VM em funcionamento em Azure. Os custos são agregados para todos os VMs do grupo avaliado. Você pode perfurar para ver detalhes de custos para VMs específicos.

> [!NOTE]
> As estimativas de custos baseiam-se nas recomendações de tamanho para uma máquina, nos seus discos e nas suas propriedades. As estimativas são para executar os VMs no local como IaaS VMs. A avaliação do servidor Azure Migrate não considera os custos do PaaS ou do SaaS.

Os custos de armazenamento agregados para o grupo avaliado são divididos em diferentes tipos de discos de armazenamento. 

### <a name="review-confidence-rating"></a>Rever a classificação de confiança

A Azure Migrate Server Assessment atribui uma classificação de confiança a uma avaliação baseada no desempenho, de uma estrela (mais baixa) para cinco estrelas (mais alta).

![Classificação de confiança](./media/tutorial-assess-vmware/confidence-rating.png)

A classificação de confiança ajuda-o a estimar a fiabilidade das recomendações de tamanho da avaliação. A classificação baseia-se na disponibilidade de pontos de dados necessários para calcular a avaliação:

**Disponibilidade de pontos de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 estrela
21%-40% | 2 estrelas
41%-60% | 3 estrelas
61%-80% | 4 estrelas
81%-100% | 5 estrelas

[Saiba mais sobre as melhores práticas](best-practices-assessment.md#best-practices-for-confidence-ratings) para as classificações de confiança.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configura um aparelho Azure Migrate. Também criou e reviu uma avaliação.

Para aprender a migrar VMware VMs para Azure usando a migração do servidor Azure Migrate, continue para o terceiro tutorial da série:

> [!div class="nextstepaction"]
> [Migrar VMs VMware](./tutorial-migrate-vmware.md)
