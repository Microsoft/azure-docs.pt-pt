---
title: Avaliar VMs de Hyper-V para migração para o Azure com o Azure Migrate | Documentos da Microsoft
description: Descreve como avaliar VMs de Hyper-V no local para migração para o Azure com o Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 83567a45980b29931f9b68bd6d60df0d427b09de
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813025"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Avaliar as VMs de Hyper-V com o Azure Migrate a avaliação de servidor

Este artigo mostra-lhe como avaliar VMs de Hyper-V no local, utilizando o Azure Migrate: Ferramenta de avaliação do servidor.

[O Azure Migrate](migrate-services-overview.md) fornece um hub de ferramentas que ajudam a detetar, avaliar e migrar cargas de trabalho, aplicações e infraestrutura para o Microsoft Azure. O hub inclui as ferramentas do Azure Migrate e ofertas de (ISV fornecedor) de terceiros independentes de software.



Este tutorial é a segunda de uma série que demonstra como avaliar e migrar VMs de Hyper-V para o Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure um projeto do Azure Migrate.
> * Configurar e registar uma aplicação do Azure Migrate.
> * Inicie deteção contínua de VMs no local.
> * Agrupar VMs detetadas e avaliar o grupo.
> * Reveja a avaliação.

> [!NOTE]
> Tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que pode configurar rapidamente a uma prova de conceito. Tutoriais utilizam as opções predefinidas, sempre que possível e não mostram todas as configurações possíveis e caminhos. Para obter instruções detalhadas, consulte os artigos de procedimentos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- [Completa](tutorial-prepare-hyper-v.md) o primeiro tutorial nesta série. Caso contrário, as instruções deste tutorial não funcionarão.
- Eis o que deveria ter feito no primeiro tutorial:
    - [Configurar as permissões do Azure](tutorial-prepare-hyper-v.md#prepare-azure) para o Azure Migrate.
    - [Preparar Hyper-V](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-assessment) clusters de anfitriões e VMs para avaliação.

## <a name="set-up-an-azure-migrate-project"></a>Configurar um projeto do Azure Migrate

1. No portal do Azure > **todos os serviços**, procure **Azure Migrate**.
2. Nos resultados da pesquisa, selecione **do Azure Migrate**.
3. Na **descrição geral**, em **detetar, avaliar e migrar servidores**, clique em **avaliar e migrar servidores de**.

    ![Detetar e avaliar os servidores](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. Na **introdução**, clique em **adicionar ferramentas**.
5. Na **migrar projeto** separador, selecione a sua subscrição do Azure e criar um grupo de recursos se não tiver uma.
6. Na **detalhes do projeto**, especifique o nome do projeto e a região na qual pretende criar o projeto.


    ![Criar um projeto do Azure Migrate](./media/tutorial-assess-hyper-v/migrate-project.png)

    Pode criar um projeto do Azure Migrate nestas regiões.

    **Geografia** | **Região**
    --- | ---
    Ásia  | Sudeste Asiático
    Europa | Europa do Norte ou da Europa Ocidental
    Reino Unido |  Sul do Reino Unido ou oeste do Reino Unido
    Estados Unidos | E.U.A. leste, E.U.A. oeste 2 ou EUA Centro-Oeste

    - A região de projeto é utilizada apenas para armazenar os metadados recolhidos das VMs no local.
    - Pode selecionar uma região de destino do Azure diferente quando migrar as VMs. Todas as regiões do Azure são suportadas para o destino da migração.

7. Clique em **Seguinte**.
8. Na **ferramenta de avaliação Select**, selecione **Azure Migrate: Avaliação de servidor** > **próxima**.

    ![Criar um projeto do Azure Migrate](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. Na **ferramenta de migração Select**, selecione **ignorar a adição de uma ferramenta de migração por agora** > **seguinte**.
10. Na **rever + adicionar ferramentas**, reveja as definições e clique em **adicionar ferramentas**.
11. Aguarde alguns minutos para que o projeto do Azure Migrate a implementar. Será levado à página do projeto. Se não vir o projeto, pode acessá-lo da **servidores** no dashboard do Azure Migrate.




## <a name="set-up-the-appliance-vm"></a>Configurar a aplicação da VM

Avaliação de servidor migrar do Azure é executado uma simples aplicação de VM de Hyper-V.

- Este dispositivo executa a deteção de VMS e envia dados de metadados e o desempenho da VM para o Azure Migrate: Avaliação do servidor.
- Para configurar a aplicação da:
    - Transferir um VHD de Hyper-V comprimido do portal do Azure.
    - Criar a aplicação e verifique se pode ligar a avaliação do servidor de migrar do Azure.
    - Configurar a aplicação pela primeira vez e registrá-la com o projeto do Azure Migrate.

### <a name="download-the-vhd"></a>Baixe o VHD

Transfira o modelo VHD zipado para a aplicação.

1. Na **objetivos de migração** > **servidores** > **do Azure Migrate: Avaliação de servidor**, clique em **detetar**.
2. Na **detetar máquinas** > **são as suas máquinas virtualizadas?** , clique em **Sim, com o Hyper-V**.
3. Clique em **transferir** para transferir o ficheiro VHD.

    ![Transferir a VM](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Certifique-se de segurança

Verifique se o arquivo zipado é seguro, antes de o implementar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Execute o seguinte comando para gerar o hash para o VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Para obter a versão da aplicação 1.19.06.27, o hash gerado deve corresponder a estas definições.

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 3681f745fa2b0a0a6910707d85161ec5
  SHA256 | e6ca109afab9657bdcfb291c343b3e3abced9a273d25273059171f9954d25832



### <a name="create-the-appliance-vm"></a>Criar a aplicação da VM

Importar o ficheiro transferido e criar a VM.

1. Extraia o ficheiro VHD zipado para uma pasta no anfitrião Hyper-V que irá alojar a aplicação da VM. Três pastas são extraídas.
2. Abra o Gestor de Hyper-V. Na **ações**, clique em **importar Máquina Virtual**.

    ![Implementar o VHD](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. No Assistente para importar Máquina Virtual > **antes de começar**, clique em **próxima**.
3. Na **localizar pasta**, especifique a pasta que contém o VHD extraído. Clique depois em **Seguinte**.
1. Na **selecionar Máquina Virtual**, clique em **próxima**.
2. Na **escolha o tipo de importação**, clique em **copiar a máquina virtual (criar um novo ID exclusivo)** . Clique depois em **Seguinte**.
3. Na **escolha destino**, deixe a predefinição. Clique em **Seguinte**.
4. Na **pastas de armazenamento**, deixe a predefinição. Clique em **Seguinte**.
5. Na **escolher rede**, especificar o comutador virtual que irá utilizar a VM. O comutador necessita de conectividade de internet para enviar dados para o Azure.
6. Na **resumo**, reveja as definições. Em seguida, clique em **concluir**.
7. No Gestor de Hyper-V > **máquinas virtuais**, inicie a VM.


### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso da aplicação para o Azure

Certifique-se de que a aplicação da VM pode ligar à [URLs do Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

### <a name="configure-the-appliance"></a>Configurar a aplicação

Configure a aplicação pela primeira vez.

1. No Gestor de Hyper-V > **máquinas virtuais**, clique com o botão direito a VM > **Connect**.
2. Fornece o idioma, fuso horário e palavra-passe para a aplicação.
3. Abra um browser em qualquer máquina que pode ligar à VM e abrir o URL da aplicação web da aplicação: **https://*nome da aplicação ou o endereço IP*: 44368**.

   Em alternativa, pode abrir a aplicação no ambiente de trabalho da aplicação ao clicar no atalho de aplicação.
1. Na aplicação web > **configurar pré-requisitos**, efetue o seguinte procedimento:
    - **Licença**: Aceite os termos de licenciamento e leia as informações de terceiros.
    - **Conectividade**: A aplicação verifica se a VM tem acesso à internet. Se a VM utiliza um proxy:
        - Clique em **definições de Proxy**e especifique o endereço de proxy e porta de escuta, o formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização de hora**: Tempo é verificado. A hora da aplicação deve ser sincronizada com o tempo de internet para deteção de VMS para funcionarem corretamente.
    - **Instalar atualizações**: Avaliação de servidor migrar do Azure verifica se a aplicação tem as atualizações mais recentes instaladas.

### <a name="register-the-appliance-with-azure-migrate"></a>Registre-se a aplicação com o Azure Migrate

1. Clique em **iniciar sessão**. Se não aparecer, certifique-se de que ter desabilitado o Bloqueador de pop-up no browser.
2. Na nova guia, inicie sessão com as credenciais do Azure.
    - Inicie sessão com o nome de utilizador e palavra-passe.
    - Inicie sessão com um PIN não é suportada.
3. Depois de iniciar sessão com êxito na, volte para a aplicação web.
4. Selecione a subscrição na qual foi criado o projeto do Azure Migrate. Em seguida, selecione o projeto.
5. Especifique um nome para a aplicação. O nome deve ser alfanumérica com 14 caracteres ou menos.
6. Clique em **registar**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegar credenciais para VHDs de SMB

Se estiver executando VHDs em PMEs, tem de ativar a delegação de credenciais a partir da aplicação para os anfitriões de Hyper-V. Se não fizer isso em cada anfitrião no [tutorial anterior](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts), efetue este agora a partir da aplicação:

1. Na aplicação da VM, execute este comando. HyperVHost1/HyperVHost2 são nomes de anfitrião de exemplo.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. Em alternativa, fazê-lo no Editor de políticas de grupo de Local na aplicação da:
    - Na **política de computador Local** > **configuração do computador**, clique em **modelos administrativos** > **sistema**  >  **Delegação de credenciais**.
    - Faça duplo clique em **permitir a delegação das credenciais de raiz**e selecione **ativado**.
    - Na **opções**, clique em **mostrar**, e adicione cada anfitrião de Hyper-V que pretende detetar à lista, com **wsman /** como um prefixo.
    - Na **delegação de credenciais**, faça duplo clique em **permitir delegação das credenciais de raiz com a autenticação de servidor somente de NTLM**. Adicionar mais uma vez, cada anfitrião de Hyper-V que pretende detetar à lista, com **wsman /** como um prefixo.

## <a name="start-continuous-discovery"></a>Iniciar a deteção contínua

Ligar a partir da aplicação para anfitriões Hyper-V ou clusters e iniciar a deteção VM.

1. Na **nome de utilizador** e **palavra-passe**, especifique as credenciais da conta que a aplicação irá utilizar para detetar VMs. Especifique um nome amigável para as credenciais e clique em **guardar os detalhes**.
2. Clique em **Adicionar anfitrião**e especifique os detalhes do anfitrião/cluster de Hyper-V.
3. Clique em **validar**. Após a validação, é apresentado o número de VMs que podem ser detetados em cada anfitrião/cluster.
    - Se a validação falhar para um anfitrião, rever o erro ao pairar o rato sobre o ícone na **estado** coluna. Corrigir problemas e validar novamente.
    - Para remover anfitriões ou clusters, selecione > **eliminar**.
    - Não é possível remover um anfitrião específico de um cluster. Só é possível remover todo o cluster.
    - Pode adicionar um cluster, mesmo se existirem problemas com a anfitriões específicos do cluster.
4. Após a validação, clique em **guardar e iniciar a deteção** para iniciar o processo de deteção.

Esta ação inicia a deteção. Demora cerca de 15 minutos para os metadados de VMs detetadas a aparecer no portal do Azure.

### <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Após a conclusão da deteção, pode verificar se as VMs aparecem no portal.

1. Abra o dashboard do Azure Migrate.
2. Na **do Azure Migrate - servidores** > **do Azure Migrate: Avaliação de servidor** página, clique no ícone que exibe a contagem do **detetados servidores**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Existem dois tipos de avaliações, pode executar a avaliação do servidor de migrar do Azure ao utilizar.

**Assessment** | **Detalhes** | **Dados**
--- | --- | ---
**Com base em desempenho** | Avaliações com base nos dados de desempenho recolhidos | **Tamanho VM recomendado**: Com base nos dados de utilização de CPU e memória.<br/><br/> **Recomendado o tipo de disco (standard ou premium disco gerido)** : Com base no IOPS e débito dos discos no local.
**Como no local** | Avaliações com base no dimensionamento no local. | **Tamanho VM recomendado**: Com base no tamanho VM no local<br/><br> **Recomendado o tipo de disco**: Com base na definição de tipo de armazenamento que selecionar para a avaliação.



### <a name="run-an-assessment"></a>Executar uma avaliação

Execute uma avaliação da seguinte forma:

1. Reveja os [melhores práticas](best-practices-assessment.md) para a criação de avaliações.
2. Na **servidores** > **do Azure Migrate: Avaliação de servidor**, clique em **avaliação**.

    ![Avaliar](./media/tutorial-assess-hyper-v/assess.png)

3. Na **avaliar servidores**, especifique um nome para a avaliação.
4. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades de avaliação](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. Na **selecione ou crie um grupo**, selecione **criar nova** e especifique um nome de grupo. Um grupo de coleta uma ou mais VMs em conjunto para avaliação.
4. Na **Adicionar máquinas ao grupo**, selecione as VMs para adicionar ao grupo.
5. Clique em **Criar avaliação** para criar o grupo e execute a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Após a criação da avaliação, visualize-a em **servidores** > **Azure Migrate: Avaliação de servidor**.
7. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.


## <a name="review-an-assessment"></a>Reveja uma avaliação

Descreve uma avaliação:

- **Preparação para o Azure**: Se as VMs são adequadas para migração para o Azure.
- **Estimativa de custo mensal**: As computação e armazenamento os custos mensais estimados para executar as VMs no Azure.
- **Estimativa de custos de armazenamento mensal**: Custos estimados para o armazenamento de disco após a migração.


### <a name="view-an-assessment"></a>Ver uma avaliação

1. Na **objetivos de migração** >  **servidores** > **Azure Migrate: Avaliação de servidor**, clique em **avaliações**.
2. Na **avaliações**, clique numa avaliação para abri-lo.

    ![Resumo da avaliação](./media/tutorial-assess-hyper-v/assessment-summary.png)


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

![Classificação de confiança](./media/tutorial-assess-hyper-v/confidence-rating.png)

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





## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Configurar uma aplicação do Azure Migrate
> * Criado e revisados uma avaliação

Avance para o terceiro tutorial da série, para saber como migrar VMs de Hyper-V para o Azure com o Azure Migrate migração do servidor.

> [!div class="nextstepaction"]
> [Migrar VMs de Hyper-V](./tutorial-migrate-hyper-v.md)
