---
title: Avaliar VMs Hiper-V para migração para Azure com Azure Migrate Microsoft Docs
description: Descreve como avaliar em locais Hiper-V VMs para migração para Azure usando Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 53cf4eea4bfe61951be9975bacf9adb2b3fcf435
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85106491"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Avaliar VMs hiper-V com avaliação do servidor Azure Migrate

Este artigo mostra-lhe como avaliar em locais Hiper-VMs, utilizando a ferramenta [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool)


Este tutorial é o segundo de uma série que demonstra como avaliar e migrar Hiper-VMs para Azure. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Crie um projeto Azure Migrate.
> * Instale e registe um aparelho Azure Migrate.
> * Inicie a descoberta contínua de VMs no local.
> * O grupo descobriu VMs e avaliou o grupo.
> * Reveja a avaliação.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa configurar rapidamente uma prova de conceito. Os tutoriais usam opções padrão sempre que possível e não mostram todas as definições e caminhos possíveis. Para obter instruções detalhadas, reveja os artigos "Como-a-fazer".

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- [Complete](tutorial-prepare-hyper-v.md) o primeiro tutorial desta série. Se não o fizeres, as instruções deste tutorial não funcionam.
- Eis o que devias ter feito no primeiro tutorial:
    - [Prepare o Azure](tutorial-prepare-hyper-v.md#prepare-azure) para trabalhar com a Azure Migrate.
    - Prepare anfitriões [Hiper-V](tutorial-prepare-hyper-v.md#prepare-for-assessment) e avaliação de VMs.
    - [Verifique](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) o que precisa para colocar o aparelho Azure Migrate para avaliação de Hiper-V.

## <a name="set-up-an-azure-migrate-project"></a>Criar um projeto Azure Migrate

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Nos resultados da pesquisa, **selecione Azure Migrate**.
3. Na **Descrição geral**, em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.

    ![Descubra e avalie servidores](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. Em **Introdução**, clique em **Adicionar ferramentas**.
5. No separador **projeto Migrate,** selecione a subscrição do Azure e crie um grupo de recursos se não tiver um.
6. No **Projeto Detalhes,** especifique o nome do projeto e a região em que pretende criar o projeto. Reveja geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

    - A região do projeto é utilizada apenas para armazenar os metadados recolhidos a partir de VMs no local.
    - Pode selecionar uma região alvo de Azure diferente quando migrar os VMs. Todas as regiões de Azure são apoiadas para o objetivo de migração.

    ![Criar um projeto Azure Migrate](./media/tutorial-assess-hyper-v/migrate-project.png)

7. Clique em **Seguinte**.
8. Na **ferramenta de avaliação Select**, selecione **Azure Migrate: Avaliação do servidor**Em  >  **seguida**.

    ![Criar um projeto Azure Migrate](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
10. Em **Rever + adicionar ferramentas,** rever as definições e clicar em Adicionar **ferramentas**.
11. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Será encaminhado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.

## <a name="set-up-the-azure-migrate-appliance"></a>Configurar o aparelho Azure Migrate


Azure Migrate:A Avaliação do Servidor utiliza um aparelho Azure Migrate leve. O aparelho realiza a descoberta de VM e envia metadados VM e dados de desempenho para a Azure Migrate. O aparelho pode ser montado de várias maneiras.

- Instale-se num Hyper-VM usando um Hiper-V VHD descarregado. Este é o método usado neste tutorial.
- Instale-se num Hiper-VM ou numa máquina física com um script instalador PowerShell. [Este método](deploy-appliance-script.md) deve ser usado se não puder configurar um VM usando o VHD, ou se estiver no Governo Azure.

Depois de criar o aparelho, verifique se pode ligar-se ao Azure Migrate:Server Assessment, configurá-lo pela primeira vez e registá-lo com o projeto Azure Migrate.

### <a name="download-the-vhd"></a>Descarregue o VHD

Descarregue o modelo VHD com fecho para o aparelho.

1. In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, click **.click**.
2. In **Discover machines**  >  **Are your machines virtualized?** **Yes, with Hyper-V**
3. Clique **em Baixar** para descarregar o ficheiro VHD.

    ![Baixar VM](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verificar segurança

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.

2. Executar o seguinte comando PowerShell para gerar o haxixe para o ficheiro ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  Verifique as versões mais recentes do aparelho e os valores do haxixe:

    - Para a nuvem pública de Azure:

        **Cenário** | **Transferir** | **SHA256**
        --- | --- | ---
        Hiper-V (8,93 GB) | [Versão mais recente](https://aka.ms/migrate/appliance/hyperv) |  572be425ea0aca69aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa8658c950bc319bdbeb931bb93b440577264500091c846a1

    - Para o Governo de Azure:

        **Cenário*** | **Transferir** | **SHA256**
        --- | --- | ---
        Hiper-V (63,1 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae4689408e43ab55ff397b77200b92121972e683f9aa3


### <a name="create-the-appliance-vm"></a>Criar o aparelho VM

Importe o ficheiro descarregado e crie o VM.

1. Depois de descarregar o ficheiro VHD com fecho para o anfitrião Hyper-V no qual o aparelho VM será colocado, extraia o ficheiro com fecho.
    - No local extraído, o ficheiro abre-se numa pasta chamada **AzureMigrateAppliance_VersionNumber**.
    - Esta pasta contém uma sub-dobragem, também chamada **AzureMigrateAppliance_VersionNumber**.
    - Esta sub-dobradeira contém mais três sub-dobradeiras - **Snapshots,** **Discos Rígidos Virtuais**e **Máquinas Virtuais**.

2. Abra o Gestor de Hyper-V. Em **Ações,** clique em **Import Virtual Machine**.

    ![Implementar VHD](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. No > do assistente de máquina virtual de importação **Antes de começar,** clique em **seguida**.
3. Na **Pasta Localizar,** selecione a pasta **Máquinas Virtuais.** Em seguida, clique em **Seguinte**.
1. Na **Máquina Virtual Selecionada,** clique em **Seguinte**.
2. No **Choose Import Type,** clique **em Copiar a máquina virtual (crie um novo ID único)**. Em seguida, clique em **Seguinte**.
3. No **Destino Escolha,** deixe a definição predefinida. Clique em **Seguinte**.
4. Nas **pastas de armazenamento,** deixe a definição predefinida. Clique em **Seguinte**.
5. In **Choose Network**, especifique o interruptor virtual que o VM irá utilizar. O switch precisa de conectividade na Internet para enviar dados para o Azure. [Saiba](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) como criar um interruptor virtual.
6. Em **Resumo,** reveja as definições. Em seguida, clique em **Concluir**.
7. No Hyper-V Manager > **Máquinas Virtuais,** inicie o VM.


## <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o aparelho VM pode ligar-se aos URLs Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)

### <a name="configure-the-appliance"></a>Configure o aparelho

Coloque o aparelho pela primeira vez.

> [!NOTE]
> Se configurar o aparelho utilizando um [script PowerShell](deploy-appliance-script.md) em vez do VHD descarregado, os dois primeiros passos deste procedimento não são relevantes.

1. No Hyper-V Manager > **Máquinas Virtuais,** clique com o botão direito no VM > **Connect**.
2. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
3. Abra um browser em qualquer máquina que possa ligar ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho clicando no atalho da aplicação.
1. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
    - **Licença**: Aceite os termos da licença e leia as informações de terceiros.
    - **Conectividade**: A aplicação verifica se o VM tem acesso à Internet. Se o VM utilizar um representante:
      - Clique **nas definições de Procuração**e especifique o endereço de procuração e a porta de audição, no formulário http://ProxyIPAddress ou http://ProxyFQDN .
      - Especifique as credenciais se o proxy precisar de autenticação.
      - Apenas é suportado o proxy HTTP.
    - **Sincronização temporal:** O tempo é verificado. O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta de VM funcione corretamente.
    - **Instalar atualizações**: A Azure Migrate Server Assessment verifica se o aparelho tem as últimas atualizações instaladas.

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Clique **em iniciar sessão**. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.
2. No novo separador, inscreva-se utilizando as suas credenciais Azure.
    - Inscreva-se com o seu nome de utilizador e senha.
    - O s-in com um PIN não é suportado.
3. Depois de iniciar sessão com sucesso, volte à aplicação web.
4. Selecione a subscrição em que o projeto Azure Migrate foi criado. Em seguida, selecione o projeto.
5. Especifique um nome para o aparelho. O nome deve ser alfanumérico com 14 caracteres ou menos.
6. Clique em **Registar**.


### <a name="delegate-credentials-for-smb-vhds"></a>Credenciais de delegado para VHDs SMB

Se estiver a executar VHDs em SMBs, deve ativar a delegação de credenciais do aparelho para os anfitriões Hiper-V. Para tal, permite que cada hospedeiro atue como delegado do aparelho. Se seguiu os tutoriais por ordem, fê-lo no tutorial anterior, quando preparou o Hyper-V para avaliação e migração. Você deveria ter configurado CredSSP para os anfitriões [manualmente](tutorial-prepare-hyper-v.md#enable-credssp-to-delegate-credentials), ou [executando um script](tutorial-prepare-hyper-v.md#run-the-script) que faz isso.

Ativar o aparelho da seguinte forma:

#### <a name="option-1"></a>Opção 1

No aparelho VM, este comando. HyperVHost1/HyperVHost2 são nomes de anfitriões exemplo.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
```

Exemplo: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Opção 2

Em alternativa, faça-o no Editor de Política do Grupo Local sobre o aparelho:

1. Na **Local Computer Policy**  >  **configuração**do computador local, clique na delegação de credenciais **de sistema de modelos**  >  **System**  >  **administrativos**.
2. Clique duas **vezes Em permitir a delegação de credenciais frescas**e selecione **Ativado**.
3. Em **Opções**, clique em **Mostrar**, e adicione cada anfitrião Hyper-V que pretende descobrir na lista, com **wsman/** como prefixo.
4. Em seguida, na Delegação de **Credenciais,** clique duplo **Deixe delegar credenciais frescas com autenticação do servidor apenas NTLM**. Mais uma vez, adicione cada anfitrião Hyper-V que pretende descobrir na lista, com **wsman/** como prefixo.

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Ligue do aparelho a anfitriões ou agrupamentos Hiper-V e inicie a descoberta de VM.

1. No **nome de utilizador** e na **palavra-passe,** especifique as credenciais de conta que o aparelho utilizará para descobrir VMs. Especifique um nome amigável para as credenciais e clique em **Guardar detalhes.**
2. Clique **em Adicionar anfitrião**e especifique detalhes do anfitrião/cluster hyper-V.
3. Clique em **Validar**. Após a validação, é indicado o número de VMs que podem ser descobertos em cada hospedeiro/cluster.
    - Se a validação falhar para um anfitrião, reveja o erro pairando sobre o ícone na coluna **Status.** Corrija problemas e valide novamente.
    - Para remover anfitriões ou agrupamentos, selecione > **Eliminar**.
    - Não se pode remover um hospedeiro específico de um aglomerado. Só se pode remover todo o aglomerado.
    - Pode adicionar um cluster, mesmo que existam problemas com anfitriões específicos no cluster.
4. Após validação, clique em **Guardar e iniciar** a descoberta para iniciar o processo de descoberta.

Isto começa a ser descoberto. Demora cerca de 1,5 minutos por anfitrião para que os metadados dos servidores descobertos apareçam no portal Azure.

### <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Após o fim da descoberta, pode verificar se os VMs aparecem no portal.

1. Abra o painel Azure Migrate.
2. Em **Azure Migrate - Servidores**  >  **Azure Migrate:** Página de Avaliação do servidor, clique no ícone que exibe a contagem para **servidores descobertos**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Existem dois tipos de avaliações que pode ser executado usando a Avaliação do Servidor Azure Migrate.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações com base em dados de desempenho recolhidos | **Tamanho VM recomendado**: Com base em dados de CPU e utilização da memória.<br/><br/> **Tipo de disco recomendado (disco gerido standard ou premium)**: Baseado no IOPS e na produção dos discos no local.
**Como no local** | Avaliações baseadas no dimensionamento no local. | **Tamanho VM recomendado**: Com base no tamanho VM no local<br/><br> **Tipo de disco recomendado**: Com base na definição do tipo de armazenamento que seleciona para a avaliação.



### <a name="run-an-assessment"></a>Executar uma avaliação

E executar uma avaliação da seguinte forma:

1. Reveja as [melhores práticas](best-practices-assessment.md) para criar avaliações.
2. Nos **servidores**  >  **Azure Migrate: Avaliação do servidor,** clique em **Avaliar**.

    ![Avaliar](./media/tutorial-assess-hyper-v/assess.png)

3. Nos **Servidores de Avaliação,** especifique um nome para a avaliação.
4. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades de avaliação](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. Em **Select ou criar um grupo**, selecione Create **New**, e especifique um nome de grupo. Um grupo reúne um ou mais VMs para avaliação.
4. No **Adicionar máquinas ao grupo,** selecione VMs para adicionar ao grupo.
5. Clique **em Criar Avaliação** para criar o grupo e executar a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Após a avaliação ser criada, veja-a nos **Servidores**  >  **Azure Migrate: Avaliação do servidor**.
7. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.


## <a name="review-an-assessment"></a>Rever uma avaliação

Uma avaliação descreve:

- **Prontidão azul**: Se os VM são adequados para a migração para Azure.
- **Estimativa mensal dos custos**: Os custos estimados mensalmente de cálculo e armazenamento para a execução dos VMs em Azure.
- **Estimativa mensal dos custos de armazenamento**: Custos estimados para o armazenamento do disco após a migração.


### <a name="view-an-assessment"></a>Ver uma avaliação

1. Nos **objetivos de migração**  >   **Servers**  >  **Azure Migrate: Avaliação do servidor,** clique em **Avaliações**.
2. Em **Avaliações,** clique numa avaliação para abri-la.

    ![Resumo da avaliação](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Rever prontidão Azure

1. Em **Azure prontidão**, verifique se os VMs estão prontos para a migração para Azure.
2. Reveja o estado de VM:
    - **Pronto para Azure**: A azure Migrate recomenda uma estimativa de tamanho e custo de VM para VMs na avaliação.
    - **Pronto com condições**: Apresenta problemas e sugeriu remediação.
    - **Não está pronto para o Azure**: Apresenta problemas e sugeriu a reparação.
    - **Prontidão desconhecida**: Usado quando a Azure Migrate não consegue avaliar a prontidão, devido a problemas de disponibilidade de dados.

2. Clique num estado **de prontidão Azure.** Pode ver detalhes de prontidão em VM e perfurar para ver detalhes de VM, incluindo configurações de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Rever detalhes de custos

Esta visão mostra o custo estimado de cálculo e armazenamento de VMs em execução em Azure.

1. Reveja os custos mensais de computação e armazenamento. Os custos são agregados para todos os VMs do grupo avaliado.

    - As estimativas de custos baseiam-se nas recomendações de tamanho para uma máquina, e nos seus discos e propriedades.
    - São apresentados custos mensais estimados para o cálculo e armazenamento.
    - A estimativa de custos é para executar os VMs no local como IaaS VMs. A avaliação do servidor Azure Migrate não considera os custos do PaaS ou do SaaS.

2. Pode rever as estimativas mensais de custos de armazenamento. Esta visão mostra os custos de armazenamento agregados para o grupo avaliado, divididos sobre diferentes tipos de discos de armazenamento.
3. Pode perfurar para ver detalhes para VMs específicos.


### <a name="review-confidence-rating"></a>Rever a classificação de confiança

Quando faz avaliações baseadas no desempenho, é atribuída uma classificação de confiança à avaliação.

![Classificação de confiança](./media/tutorial-assess-hyper-v/confidence-rating.png)

- É atribuída uma classificação de 1 estrela (mais baixa) a 5 estrelas (mais alta).
- A classificação de confiança ajuda-o a estimar a fiabilidade das recomendações de tamanho fornecidas pela avaliação.
- A classificação de confiança baseia-se na disponibilidade de pontos de dados necessários para calcular a avaliação.

As classificações de confiança para uma avaliação são as seguintes.

**Disponibilidade de pontos de dados** | **Classificação de confiança**
--- | ---
0%-20% | 1 Estrela
21%-40% | 2 Estrelas
41%-60% | 3 Estrelas
61%-80% | 4 Estrelas
81%-100% | 5 Estrelas

[Saiba mais](best-practices-assessment.md#best-practices-for-confidence-ratings) sobre as melhores práticas para as classificações de confiança.





## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Crie um aparelho Azure Migrate
> * Criei e revendo uma avaliação

Continue até ao terceiro tutorial da série, para aprender a migrar VMs Hiper-V para Azure com Azure Migrate Server Migration.

> [!div class="nextstepaction"]
> [Migrar VMs Hyper-V](./tutorial-migrate-hyper-v.md)
