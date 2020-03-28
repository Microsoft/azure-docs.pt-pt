---
title: Avaliar Os VMs Hiper-V para migração para Azure com o Azure Migrate Microsoft Docs
description: Descreve como avaliar no local os VMs hiper-V para migração para Azure usando a Avaliação do Servidor Migratório Azure.
ms.topic: tutorial
ms.date: 03/23/2020
ms.custom: mvc
ms.openlocfilehash: cb3c29e01b7917a6d639b6b2a53fc2842efc2172
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80336780"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Avaliar VMs hiper-V com avaliação do servidor migratório Azure

Este artigo mostra-lhe como avaliar no local Os VMs Hiper-V, utilizando a ferramenta de avaliação de [migração de emigração Azure:Server.](migrate-services-overview.md#azure-migrate-server-assessment-tool)


Este tutorial é o segundo de uma série que demonstra como avaliar e migrar VMs Hiper-V para Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um projeto Azure Migrate.
> * Instale e registe um aparelho Azure Migrate.
> * Inicie a descoberta contínua de VMs no local.
> * O grupo descobriu vMs e avaliou o grupo.
> * Reveja a avaliação.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa rapidamente configurar uma prova de conceito. Os tutoriais usam opções padrão sempre que possível, e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, reveja os artigos como fazer.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

- [Complete](tutorial-prepare-hyper-v.md) o primeiro tutorial desta série. Se não o fizeres, as instruções neste tutorial não funcionarão.
- Eis o que deviater feito no primeiro tutorial:
    - [Prepare Azure](tutorial-prepare-hyper-v.md#prepare-azure) para trabalhar com a Azure Migrate.
    - Prepare os anfitriões [hyper-V](tutorial-prepare-hyper-v.md#prepare-hyper-v-for-assessment) e a avaliação de VMs.
    - [Verifique](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) o que precisa para utilizar o aparelho Azure Migrate para avaliação de Hyper-V.

## <a name="set-up-an-azure-migrate-project"></a>Criar um projeto Azure Migrate

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Nos resultados da pesquisa, selecione **Azure Migrate**.
3. Na **Descrição geral**, em **Descobrir, avaliar e migrar servidores**, clique em **Avaliar e migrar servidores**.

    ![Descubra e avalie servidores](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. Em **Introdução**, clique em **Adicionar ferramentas**.
5. No **separador de projeto Migrate,** selecione a sua subscrição Azure e crie um grupo de recursos se não tiver um.
6. Em Detalhes do **Projeto,** especifique o nome do projeto e a região em que pretende criar o projeto. [Reveja](migrate-support-matrix.md#supported-geographies) as regiões em que pode criar o projeto Azure Migrate.

    - A região do projeto é usada apenas para armazenar os metadados recolhidos a partir de VMs no local.
    - Pode selecionar uma região-alvo azure diferente quando migrar os VMs. Todas as regiões de Azure são apoiadas para o objetivo de migração.

    ![Criar um projeto Azure Migrate](./media/tutorial-assess-hyper-v/migrate-project.png)

7. Clique em **Seguinte**.
8. Na **ferramenta de avaliação Select,** selecione **Azure Migrate: Server Assessment** > **Next**.

    ![Criar um projeto Azure Migrate](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. Em **Selecionar ferramenta de migração**, selecione **Ignorar a adição de uma ferramenta de migração por agora** > **Seguinte**.
10. Em **Rever + adicionar ferramentas,** reveja as definições e clique em Adicionar **ferramentas**.
11. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado. Será encaminhado para a página do projeto. Se não vir o projeto, poderá aceder ao mesmo em **Servidores** no dashboard do Azure Migrate.

## <a name="set-up-the-azure-migrate-appliance"></a>Instale o aparelho Migratório Azure

Azure Migrate:server Assessment usa um aparelho ligeiro de migração Azure. O aparelho realiza a descoberta de VM e envia metadados VM e dados de desempenho para a Migração Azure.
- O aparelho pode ser instalado num VM Hiper-V utilizando um VHD Hiper-V descarregado. Em alternativa, pode configurar o aparelho numa VM ou numa máquina física com um script de instalação PowerShell.
- Este tutorial usa o VHD. Reveja [este artigo](deploy-appliance-script.md) se quiser configurar o aparelho com um script.

Depois de criar o aparelho, verifique se pode ligar-se à Avaliação do Servidor Azure Migrate:Server, configurá-lo pela primeira vez e registá-lo com o projeto Azure Migrate.

### <a name="download-the-vhd"></a>Descarregue o VHD

Descarregue o modelo VHD com fecho para o aparelho.

1. Em **Objetivos** > de Migração**Servidores** > **Azure Migrar: Avaliação do servidor,** clique **em Descobrir**.
2. Em **Discover machines** > **As suas máquinas estão virtualizadas?** **Yes, with Hyper-V**
3. Clique em **Baixar** para descarregar o ficheiro VHD.

    ![Baixar VM](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verificar a segurança

Verifique se o ficheiro com fecho está seguro, antes de o implantar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.

2. Executar o seguinte comando PowerShell para gerar o haxixe para o ficheiro ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  Para a versão do aparelho 2.19.07.30, o hash gerado deve coincidir com estas definições.

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950 a.C.
  SHA256 | 37b3f27bc44475872e355f04fcb8f38606c84534c117d1609f2d12444569b31

### <a name="create-the-appliance-vm"></a>Crie o vm do aparelho

Importe o ficheiro descarregado e crie o VM.

1. Depois de descarregar o ficheiro VHD com fecho para o hospedeiro Hyper-V no qual o vM do aparelho será colocado, extrai o ficheiro zipped.
    - No local extraído, o ficheiro desliga-se numa pasta chamada **AzureMigrateAppliance_VersionNumber**.
    - Esta pasta contém uma subpasta, também chamada **AzureMigrateAppliance_VersionNumber**.
    - Esta subpasta contém mais três subpastas - **Snapshots,** **Discos Rígidos Virtuais**e **Máquinas Virtuais**.

2. Abra o Gestor de Hyper-V. Em **Ações,** clique **em Importar Máquina Virtual**.

    ![Implementar VHD](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. No > de Máquina Virtual importar antes de **começar,** clique em **Seguinte**.
3. Na **pasta Localizar,** selecione a pasta **Máquinas Virtuais.** Em seguida, clique em **Seguinte**.
1. Na **selecione Máquina Virtual,** clique em **Seguinte**.
2. Em **Escolher o Tipo de Importação,** clique em copiar a máquina virtual **(criar um novo ID único)**. Em seguida, clique em **Seguinte**.
3. No **Destino Escolha,** deixe a definição predefinida. Clique em **Seguinte**.
4. Nas **pastas de armazenamento,** deixe a definição predefinida. Clique em **Seguinte**.
5. Na **Rede Escolha,** especifique o interruptor virtual que o VM utilizará. O interruptor precisa de conectividade de internet para enviar dados para o Azure. [Aprenda](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) a criar um interruptor virtual.
6. Em **resumo,** reveja as definições. Em seguida, clique em **Concluir**.
7. Em Hyper-V Manager > **Máquinas Virtuais,** inicie o VM.


### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o VM do aparelho pode ligar-se a [URLs Azure](migrate-appliance.md#url-access).

### <a name="configure-the-appliance"></a>Configure o aparelho

Instale o aparelho pela primeira vez.

> [!NOTE]
> Se configurar o aparelho utilizando um [script PowerShell](deploy-appliance-script.md) em vez do VHD descarregado, os dois primeiros passos deste procedimento não são relevantes.

1. Em Hyper-V Manager > **Máquinas Virtuais,** clique à direita no VM > **Connect**.
2. Forneça o idioma, o fuso horário e a palavra-passe para o aparelho.
3. Abra um navegador em qualquer máquina que possa ligar-se ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho do aparelho clicando no atalho da aplicação.
1. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
    - **Licença**: Aceite os termos da licença e leia as informações de terceiros.
    - **Conectividade**: A aplicação verifica se o VM tem acesso à Internet. Se o VM utilizar um proxy:
      - Clique em **definições proxy**, e especifique o endereço proxy e a porta de escuta, no formulário http://ProxyIPAddress ou http://ProxyFQDN.
      - Especifique as credenciais se o proxy precisar de autenticação.
      - Apenas é suportado o proxy HTTP.
    - **Sincronização de tempo**: O tempo é verificado. O tempo no aparelho deve estar sincronizado com o tempo de internet para que a descoberta de VM funcione corretamente.
    - **Instalar atualizações**: A Avaliação do Servidor Migratório Azure verifica se o aparelho tem as últimas atualizações instaladas.

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Clique **em iniciar sessão**. Se não aparecer, certifique-se de que desativou o bloqueador pop-up no navegador.
2. No novo separador, inscreva-se utilizando as suas credenciais Azure.
    - Inscreva-se com o seu nome de utilizador e senha.
    - O sessão com um PIN não é suportado.
3. Depois de iniciar sessão com sucesso, volte para a aplicação web.
4. Selecione a subscrição em que foi criado o projeto Azure Migrate. Em seguida, selecione o projeto.
5. Especifique um nome para o aparelho. O nome deve ser alfanumérico com 14 caracteres ou menos.
6. Clique no **Registo**.


### <a name="delegate-credentials-for-smb-vhds"></a>Credenciais de delegado para VHDs SMB

Se estiver a executar VHDs em SMBs, deve ativar a delegação de credenciais do aparelho para os anfitriões Hyper-V. Isto requer o seguinte:

- Permite que cada hospedeiro aja como delegado do aparelho. Se seguiu os tutoriais em ordem, fê-lo no tutorial anterior, quando preparou o Hyper-V para avaliação e migração. Você deve ter configurado CredSSP para os anfitriões [manualmente](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts), ou [executando um script](tutorial-prepare-hyper-v.md#prepare-with-a-script) que faz isso.
- Habilitar a delegação da CredSSP para que o aparelho Azure Migrate possa funcionar como cliente, delegando credenciais a um hospedeiro.

Ativar o aparelho da seguinte forma:

#### <a name="option-1"></a>Opção 1

No vM do aparelho, execute este comando. HyperVHost1/HyperVHost2 são nomes de anfitriões exemplo.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Exemplo: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Opção 2

Em alternativa, faça-o no Editor de Política do Grupo Local sobre o aparelho:

1. Na**configuração**do computador de **política** > de computador local, clique em**credenciais**de**credenciais** > do sistema de **modelos administrativos** > .
2. Clique duplo **Para permitir a delegação**de novas credenciais e selecionar **Ativado**.
3. Em **Opções,** clique em **Mostrar**, e adicione cada anfitrião Hyper-V que deseja descobrir na lista, com **wsman/** como prefixo.
4. Em seguida, na **Delegação de Credenciais,** clique duplo **Permitir delegar novas credenciais com autenticação de servidor apenas NTLM**. Mais uma vez, adicione cada anfitrião Hyper-V que deseja descobrir na lista, com **wsman/** como prefixo.

## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Ligue-se do aparelho aos anfitriões ou aglomerados hyper-V e inicie a descoberta vm.

1. No **nome do utilizador** e na **palavra-passe,** especifique as credenciais de conta que o aparelho utilizará para descobrir VMs. Especifique um nome amigável para as credenciais e clique em **Guardar detalhes**.
2. Clique em **Adicionar anfitrião**e especificar detalhes do hospedeiro/cluster hyper-V.
3. Clique em **Validar**. Após validação, é mostrado o número de VMs que podem ser descobertos em cada hospedeiro/cluster.
    - Se a validação falhar para um hospedeiro, reveja o erro pairando sobre o ícone na coluna **'Estado'.** Corrija os problemas e valide novamente.
    - Para remover hospedeiros ou agrupamentos, selecione > **Eliminar**.
    - Não se pode remover um hospedeiro específico de um aglomerado. Só se pode remover todo o aglomerado.
    - Pode adicionar um cluster, mesmo que existam problemas com anfitriões específicos no cluster.
4. Após validação, clique em **Guardar e iniciar** a descoberta para iniciar o processo de descoberta.

Isto começa a ser descoberto. Leva cerca de 1,5 minutos por hospedeiro para que os metadados de servidores descobertos apareçam no portal Azure.

### <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

Após a descoberta, pode verificar se os VMs aparecem no portal.

1. Abra o painel de migração Azure.
2. No **Azure Migrate - Servers** > **Azure Migrar:** Página de Avaliação do Servidor, clique no ícone que apresenta a contagem para **servidores descobertos**.

## <a name="set-up-an-assessment"></a>Configurar uma avaliação

Existem dois tipos de avaliações que pode executar usando a Avaliação do Servidor Migratório Azure.

**Avaliação** | **Detalhes** | **Dados**
--- | --- | ---
**Baseado no desempenho** | Avaliações baseadas em dados de desempenho recolhidos | **Tamanho VM recomendado**: Baseado em CPU e dados de utilização da memória.<br/><br/> **Tipo de disco recomendado (disco gerido padrão ou premium)**: Baseado no IOPS e na entrada dos discos no local.
**Como no local** | Avaliações baseadas no dimensionamento no local. | **Tamanho VM recomendado**: Baseado no tamanho vm no local<br/><br> **Tipo**de disco recomendado : Com base na definição do tipo de armazenamento selecionada para a avaliação.



### <a name="run-an-assessment"></a>Executar uma avaliação

Eexecutar uma avaliação da seguinte forma:

1. Reveja as [melhores práticas](best-practices-assessment.md) para a criação de avaliações.
2. Nos **Servidores** > **Azure Migrar: Avaliação do servidor,** clique em **Avaliar**.

    ![Avaliar](./media/tutorial-assess-hyper-v/assess.png)

3. Em **'Avaliar Servidores',** especifique um nome para a avaliação.
4. Clique em **Ver tudo** para rever as propriedades de avaliação.

    ![Propriedades de avaliação](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. Em **Selecionar ou criar um grupo,** selecione Criar **Novo**e especifique um nome de grupo. Um grupo reúne um ou mais VMs para avaliação.
4. Em **Adicionar máquinas ao grupo,** selecione VMs para adicionar ao grupo.
5. Clique em **Criar Avaliação** para criar o grupo e executar a avaliação.

    ![Criar uma avaliação](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Após a criação da avaliação, veja-a em **Servers** > **Azure Migrate: Server Assessment**.
7. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.


## <a name="review-an-assessment"></a>Rever uma avaliação

Uma avaliação descreve:

- **Prontidão azure**: Se os VMs são adequados para migração para Azure.
- **Estimativa mensal de custos**: Os custos estimados mensais de cálculo e armazenamento para a execução dos VMs em Azure.
- **Estimativa mensal**do custo do armazenamento : Custos estimados para armazenamento de disco após migração.


### <a name="view-an-assessment"></a>Ver uma avaliação

1. Nos **objetivos** >  de migração**Servidores** > **Azure Migrar: Avaliação**do servidor, clicar em **Avaliações**.
2. Em **Avaliações,** clique numa avaliação para abri-la.

    ![Resumo da avaliação](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Rever a prontidão do Azure

1. Na **prontidão de Azure,** verifique se os VMs estão prontos para a migração para Azure.
2. Reveja o estado vm:
    - **Pronto para Azure**: A Azure Migrate recomenda uma estimativa de tamanho VM e custos para os VMs na avaliação.
    - **Pronto com condições**: Mostra problemas e sugeriu reparação.
    - **Não está pronto para o Azure**: Mostra questões e sugeriu reparação.
    - **Prontidão desconhecida**: Usado quando o Azure Migrate não consegue avaliar a prontidão, devido a problemas de disponibilidade de dados.

2. Clique num estado de **prontidão Azure.** Pode ver detalhes de prontidão vM e perfurar para ver detalhes vm, incluindo definições de computação, armazenamento e rede.

### <a name="review-cost-details"></a>Analisar detalhes de custos

Esta visão mostra o cálculo estimado e o custo de armazenamento de vMs de funcionamento em Azure.

1. Reveja os custos mensais de cálculo e armazenamento. Os custos são agregados para todos os VMs do grupo avaliado.

    - As estimativas de custos baseiam-se nas recomendações de tamanho para uma máquina, e nos seus discos e propriedades.
    - Os custos mensais estimados para a computação e armazenamento são apresentados.
    - A estimativa de custos é para executar os VMs no local como VMs IaaS. A Avaliação do Servidor Migratório Azure não considera os custos do PaaS ou do SaaS.

2. Pode rever as estimativas mensais de custos de armazenamento. Esta vista mostra os custos de armazenamento agregados para o grupo avaliado, divididos sobre diferentes tipos de discos de armazenamento.
3. Você pode perfurar para ver detalhes para VMs específicos.


### <a name="review-confidence-rating"></a>Rever a classificação de confiança

Quando executa avaliações baseadas no desempenho, uma classificação de confiança é atribuída à avaliação.

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

[Saiba mais](best-practices-assessment.md#best-practices-for-confidence-ratings) sobre as melhores práticas para avaliações de confiança.





## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Instale um aparelho Azure Migrate
> * Criou e reviu uma avaliação

Continue até ao terceiro tutorial da série, para aprender a migrar VMs Hiper-V para Azure com migração de servidores migratórios Azure.

> [!div class="nextstepaction"]
> [Migrar VMs Hyper-V](./tutorial-migrate-hyper-v.md)
