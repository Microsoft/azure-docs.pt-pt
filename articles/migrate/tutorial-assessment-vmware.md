---
title: Detetar e avaliar VMs VMware no local para migração para o Azure com o Azure Migrate | Microsoft Docs
description: Descreve como detetar e avaliar VMs VMware no local para migração para o Azure através do serviço Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: dc2fb3faddc8af30edd66b926259c05580d9872a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58104527"
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Descobrir e avaliar VMs VMware no local para migração para o Azure

O serviço [Azure Migrate](migrate-overview.md) avalia as cargas de trabalho no local para migração para o Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta que o Azure Migrate utiliza para detetar VMs no local
> * Criar um projeto do Azure Migrate.
> * Configurar uma máquina virtual (VM) do recoletor no local para detetar VMs VMware no local para avaliação.
> * Agrupar VMs e criar uma avaliação.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- **VMware**: As VMs que planeia migrar tem de ser geridas pelo vCenter Server com a versão 5.5, 6.0, 6.5 ou 6.7. Além disso, precisa de uma versão em execução de anfitrião ESXi 5.5 ou superior para implementar a VM do recoletor.
- **conta do vCenter Server**: Precisa de uma conta só de leitura para aceder ao vCenter Server. O Azure Migrate utiliza esta conta para detetar as VMs no local.
- **Permissões**: No vCenter Server, precisa de permissões para criar uma VM ao importar um ficheiro no. Formato de OVA.

## <a name="create-an-account-for-vm-discovery"></a>Criar uma conta para deteção de VMs

O Azure Migrate necessita de acesso aos servidores VMware para detetar automaticamente as VMs para avaliação. Crie uma conta VMware com as seguintes propriedades. Esta conta é especificada durante a configuração do Azure Migrate.

- Tipo de utilizador: Pelo menos um utilizador só de leitura
- Permissões: Objeto Data Center –> Propagar ao Objeto Subordinado, função=Só de Leitura
- Detalhes: Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.
- Para restringir o acesso, atribua a função Sem acesso com Propagar para o objeto subordinado aos objetos subordinados (anfitriões vSphere, arquivos de dados, VMs e redes).


## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-project"></a>Criar um projeto

1. No portal do Azure, clique em **Criar um recurso**.
2. Procure **Azure Migrate** e selecione o serviço **Azure Migrate** nos resultados da pesquisa. Em seguida, clique em **Criar**.
3. Especifique um nome de projeto e a subscrição do Azure para o projeto.
4. Crie um novo grupo de recursos.
5. Especifique a geografia na qual quer criar o projeto e, em seguida, clique em **Criar**. Só pode criar um projeto do Azure Migrate em áreas geográficas indicadas a seguir. No entanto, ainda pode planear a migração para qualquer localização do Azure. A geografia especificada para o projeto só é utilizada para armazenar os metadados recolhidos das VMs no local.

**Geografia** | **Localização de armazenamento**
--- | ---
Azure Government | Gov (US) - Virginia
Ásia | Sudeste Asiático
Europa | Europa do Norte ou da Europa Ocidental
Une Estados | E.U.A. Leste ou oeste dos E.U.A.

![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)


## <a name="download-the-collector-appliance"></a>Transferir a aplicação recoletora

O Azure Migrate cria uma VM no local, conhecida como aplicação recoletora. Esta VM deteta as VMs VMware no local e envia os metadados sobre as mesmas para o serviço Azure Migrate. Para configurar a aplicação recoletora, transfira um ficheiro .OVA e importe-o para o vCenter Server no local para criar a VM.

1. No projeto do Azure Migrate, clique em **Começar** > **Detetar e Avaliar** > **Detetar Máquinas**.
2. Na **detetar máquinas**, clique em **transferir** para transferir a aplicação.

    A aplicação do Azure Migrate comunica com o vCenter Server e perfis continuamente o ambiente no local para recolher dados de utilização em tempo real para cada VM. Recolhe os contadores de pico para cada uma (utilização da CPU, utilização da memória etc.). Este modelo não utiliza as definições de estatísticas do vCenter Server para a recolha de dados de desempenho. Pode parar a criação contínua de perfis em qualquer altura da aplicação.

    > [!NOTE]
    > A aplicação de deteção de uso individual foi agora preterida como esse método baseou-se no vCenter definições de estatísticas do servidor para a disponibilidade de ponto de dados de desempenho e coletados contadores de desempenho médio que resultou em insuficientemente dimensionamento de VMs para a migração para o Azure.

    **Avaliações rápidas:** Com a aplicação da deteção contínua, assim que estiver a deteção concluir (demora duas horas, consoante o número de VMs), pode criar avaliações de imediato. Uma vez que a recolha de dados de desempenho é iniciado quando pode iniciar a deteção, se estiver à procura de avaliações rápidas, selecione o critério de tamanho na avaliação como *como no local*. Para obter avaliações baseado no desempenho, é recomendado aguardar pelo menos um dia após iniciar deteção para obter recomendações de tamanho fiável.

    A aplicação só recolhe dados de desempenho continuamente, ele não detecta qualquer alteração de configuração no ambiente no local (ou seja, a adição de VM, eliminação, a adição de disco etc.). Se houver uma alteração de configuração no ambiente no local, pode fazer o seguinte para refletir as alterações no portal:

    - Adição de itens (VMs, discos, núcleos, etc.): Para refletir estas alterações no portal do Azure, pode parar a deteção a partir da aplicação e, em seguida, inicie-o novamente. Isto irá garantir que as alterações são atualizadas no projeto do Azure Migrate.

    - Eliminação das VMs: A forma como a aplicação foi concebida, a eliminação de VMs não será refletida, mesmo se parar e iniciar a deteção. Isto acontece porque os dados das deteções subsequentes são anexados às deteções mais antigas e não são substituídos. Neste caso, pode simplesmente ignorar a VM no portal, ao removê-la do seu grupo e recalcular a avaliação.


3. Em **Copiar as credenciais do projeto**, copie o ID e a chave do projeto. Precisará destes dados quando configurar o recoletor.

    ![Transferir o ficheiro .ova](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Verificar a aplicação recoletora

Verifique se o ficheiro .OVA é seguro, antes de implementá-lo.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Execute o comando abaixo para gerar o hash para o OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. O hash gerado deve corresponder a estas definições.

#### <a name="continuous-discovery"></a>Deteção contínua

  Para a versão ova 1.0.10.11

  **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | 5f6b199d8272428ccfa23543b0b5f600
    SHA1 | daa530de6e8674a66a728885a7feb3b0a2e8ccb0
    SHA256 | 85da50a21a7a6ca684418a87ccc1dd4f8aab30152c438a17b216ec401ebb3a21

  Para a versão ova 1.0.10.9

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 169f6449cc1955f1514059a4c30d138b
  SHA1 | f8d0a1d40c46bbbf78cd0caa594d979f1b587c8f
  SHA256 | d68fe7d94be3127eb35dd80fc5ebc60434c8571dcd0e114b87587f24d6b4ee4d

  Para a versão OVA 1.0.10.4

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
  SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
  SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be


#### <a name="one-time-discovery-deprecated-now"></a>Deteção única (agora preterida)

Esse modelo foi agora preterido, o suporte para aplicações existentes, serão disponibilizadas.

  Para a versão OVA 1.0.9.15

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
  SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
  SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

  Para a versão OVA 1.0.9.14

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
  SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
  SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

  Para a versão OVA 1.0.9.12

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | d0363e5d1b377a8eb08843cf034ac28a
  SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
  SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

## <a name="create-the-collector-vm"></a>Criar a VM do recoletor

Importe o ficheiro transferido para o vCenter Server.

1. Na consola do vSphere Client, clique em **Ficheiro** > **Implementar Modelo OVF**.

    ![Implementar OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. No Assistente para Implementar o Modelo OVF > **Origem**, especifique a localização do ficheiro .ova.
3. Em **Nome** e **Localização**, especifique um nome amigável para a VM do recoletor e o objeto de inventário em que será alojada a VM.
5. Em **Anfitrião/Cluster**, especifique o anfitrião ou cluster em que a VM do recoletor será executada.
7. No armazenamento, especifique o destino de armazenamento para a VM do recoletor.
8. Em **Formato do Disco**, especifique o tipo e o tamanho do disco.
9. Em **Mapeamento da Rede**, especifique a rede à qual se ligará a VM do recoletor. A rede necessita de conectividade à Internet para enviar metadados para o Azure.
10. Reveja e confirme as definições e, em seguida, clique em **Concluir**.

## <a name="run-the-collector-to-discover-vms"></a>Executar o recoletor para detetar VMs

1. Na consola do vSphere Client, clique com o botão direito do rato na VM > **Abrir Consola**.
2. Forneça as preferências de idioma, fuso horário e palavra-passe para a aplicação.
3. No ambiente de trabalho, clique no atalho **Executar recoletor**.
4. Clique em **Procurar atualizações**, na barra superior da IU do recoletor, e verifique se o recoletor está em execução na versão mais recente. Caso contrário, pode optar por transferir o pacote de atualização mais recente a partir da ligação e atualizar o recoletor.
5. No Recoletor do Azure Migrate, abra **Configurar pré-requisitos**.
   - Selecione a nuvem do Azure para o qual pretende migrar (Global do Azure ou do Azure Government).
   - Aceite os termos de licenciamento e leia as informações de terceiros.
   - O recoletor verifica se a VM tem acesso à Internet.
   - Se a VM acede à Internet através de um proxy, clique em **Definições de proxy** e especifique o endereço do proxy e a porta de escuta. Especifique as credenciais se o proxy precisar de autenticação. [Saiba mais](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-prerequisites) sobre os requisitos de conectividade de internet e o [lista de URLs](https://docs.microsoft.com/azure/migrate/concepts-collector) que acessa o recoletor.

     > [!NOTE]
     > O endereço de proxy tem de ser introduzido no formato <http://ProxyIPAddress> ou <http://ProxyFQDN>. Apenas é suportado o proxy HTTP. Se tiver um proxy de interceção, a ligação à internet inicialmente poderá falhar se não tiver importado o certificado de proxy; [Saiba mais](https://docs.microsoft.com/azure/migrate/concepts-collector) na forma como pode corrigir este problema ao importar o certificado de proxy como um certificado fidedigno na VM do recoletor.

   - O recoletor verifica se o serviço do recoletor está em execução. O serviço está instalado por predefinição na VM do recoletor.
   - Transfira e instale o VMware PowerCLI.

6. Em **Especificar detalhes do vCenter Server**, efetue o seguinte:
    - Especifique o nome (FQDN) ou o endereço IP do vCenter Server.
    - Em **Nome de utilizador** e **Palavra-passe**, especifique as credenciais da conta só de leitura que o recoletor utilizará para detetar VMs no vCenter Server.
    - Em **Âmbito da coleção**, selecione um âmbito para a deteção de VMs. O recoletor só pode detetar VMs dentro do âmbito especificado. O âmbito pode ser definido para uma pasta, datacenter ou cluster específicos. Não deve conter mais de 1500 VMs. [Saiba mais](how-to-scale-assessment.md) acerca da deteção de um ambiente maior.

7. Em **Especificar projeto de migração**, especifique o ID e a chave de projeto do Azure Migrate que copiou do portal. Se não os tiver copiado, abra o portal do Azure a partir da VM do recoletor. Na página **Descrição geral** do projeto, clique em **Detetar Máquinas** e copie os valores.  
8. Em **Ver o progresso da recolha**, monitorize o estado da deteção. [Saiba mais](https://docs.microsoft.com/azure/migrate/concepts-collector) acerca dos dados que são recolhidos pelo recoletor do Azure Migrate.

> [!NOTE]
> O recoletor só suporta "Inglês (Estados Unidos)" como idioma do sistema operativo e da interface do recoletor.
> Se alterar as definições num computador que pretende avaliar, acione a deteção novamente antes de executar a avaliação. No recoletor, selecione a opção **Iniciar novamente a coleção** para fazê-lo. Depois de terminar a coleção, selecione a opção **Recalcular** para a avaliação no portal, para obter os resultados da avaliação atualizada.


### <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

A aplicação recoletora vai de perfil continuamente o ambiente no local e irá enviar os dados de desempenho num intervalo de hora. Pode ver as máquinas no portal do fim de uma hora de iniciar a deteção.

1. No projeto de migração, clique em **Gerir** > **Máquinas**.
2. Verifique se as VMs que quer detetar aparecem no portal.


## <a name="create-and-view-an-assessment"></a>Criar e ver uma avaliação

Após a deteção das VMs no portal, agrupá-los e criar avaliações. Pode criar imediatamente como no local avaliações assim que as VMs são detetadas no portal. Recomenda-se de espera, pelo menos, um dia antes de criar qualquer avaliações baseado no desempenho para obter recomendações de tamanho fiável.

1. Na página **Descrição geral** do projeto, clique em **+ Criar avaliação**.
2. Clique em **Ver tudo** para rever as propriedades de avaliação.
3. Crie o grupo e especifique um nome.
4. Selecione as máquinas que quer adicionar ao grupo.
5. Clique em **Criar Avaliação**, para criar o grupo e a avaliação.
6. Após a criação da avaliação, visualize-a em **Descrição geral** > **Dashboard**.
7. Clique em **Exportar avaliação**, para transferi-la como um ficheiro do Excel.

> [!NOTE]
> É vivamente recomendado aguardar pelo menos um dia, depois de iniciar a deteção, antes de criar uma avaliação. Se quiser atualizar uma avaliação existente com os dados de desempenho mais recentes, poderá utilizar o comando **Recalcular** na avaliação para a atualizar.

### <a name="assessment-details"></a>Detalhes da avaliação

Uma avaliação inclui informações sobre se as VMs no local são compatíveis com o Azure, qual seria o tamanho de VM mais adequado para executar a VM no Azure e os custos mensais estimados do Azure.

![Relatório de avaliação](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Preparação para o Azure

A vista de preparação para o Azure na avaliação mostra o estado de preparação de cada VM. Consoante as propriedades da VM, cada VM pode ter a seguinte marcação:
- Preparado para o Azure
- Condicionalmente preparado para o Azure
- Não está pronto para o Azure
- Preparação desconhecida

Para as VMs que estão prontas, o Azure Migrate recomenda um tamanho de VM no Azure. A recomendação de tamanho feita pelo Azure Migrate depende do critério de dimensão especificado nas propriedades de avaliação. Se o critério de tamanho for o tamanho baseado no desempenho, a recomendação de tamanho será feita tendo em consideração o histórico de desempenho das VMs (CPU e memória) e dos discos (IOPS e débito). Se o critério de dimensionamento for “como no local”, o Azure Migrate não considerará os dados de desempenho da VM e dos discos. A recomendação para o tamanho da VM no Azure é feita ao observar o tamanho da VM no local e o dimensionamento de disco é efetuado com base no Tipo de armazenamento especificado nas propriedades de avaliação (a predefinição é discos premium). [Saiba mais](concepts-assessment-calculation.md) sobre como se processa o dimensionamento no Azure Migrate.

Para VMs não preparadas ou condicionalmente preparadas para o Azure, o Azure Migrate explica os problemas de preparação e fornece passos de solução.

As VMs para as quais o Azure Migrate não pode identificar a preparação para o Azure (devido à indisponibilidade de dados) estão marcadas como preparação desconhecida.

Além da preparação e do dimensionamento, o Azure Migrate também sugere ferramentas que pode utilizar para migrar a VM. Isto requer uma deteção mais aprofundada do ambiente no local. [Saiba mais](how-to-get-migration-tool.md) sobre como pode fazer uma deteção mais aprofundada ao instalar agentes nos computadores no local. Se os agentes não estiverem instalados nos computadores no local, a migração lift-and-shift é sugerida com o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Se os agentes estiverem instalados no computador no local, o Azure Migrate analisa os processos em execução dentro do computador e identifica se o computador é um computador de base de dados ou não. Se o computador for um computador de base de dados, o [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) é sugerido. Caso contrário, o Azure Site Recovery é sugerido como a ferramenta de migração.

  ![Preparação para a avaliação](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Estimativa de custo mensal

Esta vista mostra o custo total de computação e armazenamento da execução das VMs no Azure, bem como os detalhes para cada máquina. As estimativas de custos são calculadas considerando as recomendações de tamanho do Azure Migrate para uma máquina, os respetivos discos e as propriedades de avaliação.

> [!NOTE]
> A estimativa de custo fornecida pelo Azure Migrate é para executar as VMs no local como VMs de infraestrutura como um serviço (IaaS) do Azure. O Azure Migrate não considera os custos de Plataforma como serviço (PaaS) ou Software como serviço (SaaS).

Os custos mensais estimados para computação e armazenamento são agregados para todas as VMs no grupo.

![Custo de VM de avaliação](./media/tutorial-assessment-vmware/assessment-vm-cost.png)

#### <a name="confidence-rating"></a>Classificação de confiança

Cada avaliação baseada no desempenho no Azure Migrate é associada a uma classificação de confiança de 1 a 5 estrelas (sendo que 1 estrela corresponde à mais baixa e 5 à mais alta). A classificação de confiança é alocada a uma avaliação com base na disponibilidade dos pontos de dados necessários para calcular a avaliação. A classificação de confiança de uma avaliação ajuda a calcular a fiabilidade das recomendações de tamanho fornecidas pelo Azure Migrate. Classificação de confiança não é aplicável ao "como-é" avaliações locais.

Para o dimensionamento com base em desempenho, o Azure Migrate requer os dados de utilização da CPU, da memória e da VM. Além disso, para cada disco ligado à VM, é necessário o IOPS do disco e os dados de débito. De forma semelhante, para cada adaptador de rede anexado a uma VM, o Azure Migrate requer a entrada/saída de rede para efetuar o dimensionamento com base no desempenho. Se algum dos números de utilização acima não estiver disponível no vCenter Server, a recomendação de tamanho feita pelo Azure Migrate poderá não ser fiável. Consoante a percentagem de pontos de dados disponíveis, a classificação de confiança da avaliação é fornecida abaixo:

   **Disponibilidade de pontos de dados** | **Classificação de confiança**
   --- | ---
   0%-20% | 1 Estrela
   21%-40% | 2 Estrelas
   41%-60% | 3 Estrelas
   61%-80% | 4 Estrelas
   81%-100% | 5 Estrelas

Uma avaliação pode não ter todos os pontos de dados disponíveis por um dos seguintes motivos:

- Não analisou o ambiente durante o tempo para a qual está a criar a avaliação. Por exemplo, se estiver a criar a avaliação com duração de desempenho definida como um dia, terá de aguardar pelo menos um dia após iniciar a deteção para todos os pontos de dados serem recolhidos.

- Poucas VMs foram encerradas durante o período para o qual a avaliação é calculada. Se tiver desligado alguma VM durante algum tempo, não poderemos recolher os dados de desempenho correspondentes a esse período.

- Poucas VMs foram criadas no período para o qual a avaliação é calculada. Por exemplo, se estiver a criar uma avaliação para o histórico de desempenho do último mês, mas poucas VMs tiverem sido criadas no ambiente há apenas uma semana. Em casos como este, o histórico de desempenho das novas VMs não estará lá para o período completo.

> [!NOTE]
> Se a classificação de confiança de qualquer avaliação for inferior a 5 estrelas, aguarde pelo menos um dia para a aplicação para o ambiente de perfil e, em seguida *recalcular* a avaliação. Se não conseguir fazê-lo, o dimensionamento baseado em desempenho poderá não ser fiável e recomenda-se que mude para o *dimensionamento no local* ao alterar as propriedades de avaliação.

## <a name="next-steps"></a>Passos Seguintes

- [Saiba](how-to-modify-assessment.md) como personalizar uma avaliação com base nos seus requisitos.
- Saiba como criar grupos de avaliação de confiança elevada através do [mapeamento de dependência de máquinas](how-to-create-group-machine-dependencies.md)
- [Saiba mais](concepts-assessment-calculation.md) sobre como são calculadas as avaliações.
- [Saiba](how-to-scale-assessment.md) como detetar e avaliar um ambiente VMware grande.
- [Saiba mais](resources-faq.md) sobre as FAQs do Azure Migrate
