---
title: Dimensionar a deteção e avaliação com o Azure Migrate | Documentos da Microsoft
description: Descreve como avaliar um grande número de máquinas no local com o serviço Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 8a2ea64d32194ff06378e3227b260c4f10d53175
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116678"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>Detetar e avaliar um ambiente do VMware de grandes dimensões

O Azure Migrate tem um limite de 1500 máquinas por projeto, este artigo descreve como avaliar um grande número de máquinas de virtuais no local (VMs) usando [do Azure Migrate](migrate-overview.md).   

## <a name="prerequisites"></a>Pré-requisitos

- **VMware**: As VMs que planeia migrar tem de ser geridas pelo vCenter Server versão 5.5, 6.0, 6.5 ou 6.7. Além disso, terá uma versão em execução de anfitrião ESXi 5.5 ou posterior para implementar a VM do recoletor.
- **a conta do vCenter**: Precisa de uma conta só de leitura para aceder ao vCenter Server. O Azure Migrate utiliza esta conta para detetar as VMs no local.
- **Permissões**: No vCenter Server, precisa de permissões para criar uma VM ao importar um ficheiro no formato de OVA.
- **Definições das estatísticas**: Este requisito só é aplicável para o [modelo de deteção única](https://docs.microsoft.com/azure/migrate/concepts-collector) que foi despromovido agora. Para o modelo de deteção única, as definições de estatísticas do vCenter Server devem ser definidas como nível 3 antes de iniciar a implementação. O nível de estatísticas é para ser definido como 3 para cada um dos intervalos de coleção de mês, semana e dia. Se o nível for inferior a 3 para qualquer um dos intervalos de coleção de três, a avaliação funcionará, mas não serão recolhidos os dados de desempenho para armazenamento e rede. As recomendações de tamanho, em seguida, irão basear-se em dados de desempenho de CPU e memória e dados de configuração de adaptadores de rede e disco.

> [!NOTE]
> A aplicação de deteção de uso individual foi agora preterida como esse método baseou-se no vCenter definições de estatísticas do servidor para a disponibilidade de ponto de dados de desempenho e coletados contadores de desempenho médio que resultou em insuficientemente dimensionamento de VMs para a migração para o Azure.

### <a name="set-up-permissions"></a>Configurar permissões

O Azure Migrate necessita de acesso aos servidores VMware para detetar automaticamente as VMs para avaliação. A conta de VMware tem as seguintes permissões:

- Tipo de utilizador: Pelo menos um utilizador só de leitura
- Permissões: Objeto Data Center –> Propagar ao Objeto Subordinado, função=Só de Leitura
- Detalhes: Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.
- Para restringir o acesso, atribua a função Sem acesso com Propagar para o objeto subordinado aos objetos subordinados (anfitriões vSphere, arquivos de dados, VMs e redes).

Se estiver a implementar num ambiente de inquilino, eis uma forma de configurar estas definições:

1. Criar um utilizador por inquilino e utilizar [RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), atribuir permissões só de leitura para todas as VMs que pertencem a um inquilino específico. Em seguida, utilize as credenciais para a deteção. RBAC garante que o utilizador correspondente do vCenter terão acesso às VMs apenas específico de inquilino.
2. Configurar o RBAC para os utilizadores de inquilino diferente conforme descrito no exemplo a seguir para 1 utilizador e o utilizador n. º 2:

    - Na **nome de utilizador** e **palavra-passe**, especifique as credenciais de conta só de leitura que o recoletor utilizará para detetar VMs no
    - Datacenter1 - conceder permissões só de leitura ao utilizador n. º 1 e 2 a utilizador. Não propagar essas permissões para todos os objetos subordinados, porque definirá permissões em VMs individuais.

      - VM1 (inquilino n. º 1) (permissões só de leitura para o utilizador n. º 1)
      - VM2 (inquilino n. º 1) (permissões só de leitura para o utilizador n. º 1)
      - VM3 (inquilino n. º 2) (permissões só de leitura para o utilizador n. º 2)
      - VM4 (inquilino n. º 2) (permissões só de leitura para o utilizador n. º 2)

   - Se efetuar a deteção com as credenciais do utilizador n. º 1, em seguida, apenas a VM1 e a VM2 serão detetado.

## <a name="plan-your-migration-projects-and-discoveries"></a>Planear os projetos de migração e deteções

Com base no número de VMs que pretende detetar, pode criar vários projetos e implementar várias aplicações no seu ambiente. Uma aplicação pode estar conectada a um único servidor de vCenter e um único projeto, (a menos que parar a deteção e começar a gerarem novamente).

No caso de deteção única (agora preterida), a deteção funciona em de incêndio e se esqueça de modelo, depois de fazer uma deteção, pode utilizar o mesmo recoletor para recolher dados de um servidor vCenter diferente ou enviá-lo para um projeto de migração diferentes.

> [!NOTE]
> A aplicação de deteção de uso individual foi agora preterida como esse método baseou-se no vCenter definições de estatísticas do servidor para a disponibilidade de ponto de dados de desempenho e coletados contadores de desempenho médio que resultou em insuficientemente dimensionamento de VMs para a migração para o Azure. Recomenda-se para mover para a aplicação de deteção única.

Planear as suas descobertas e avaliações com base nas seguintes limites:

| **Entidade** | **Limite de máquina** |
| ---------- | ----------------- |
| Project    | 1,500             |
| Deteção  | 1,500             |
| Avaliação | 1,500             |

Considere estas considerações de planeamento:

- Ao fazer uma deteção ao utilizar o recoletor do Azure Migrate, pode definir o âmbito de deteção para uma pasta de servidor do vCenter, datacenter, cluster ou anfitrião.
- Para fazer mais do que uma deteção a partir do mesmo vCenter Server, certifique-se no vCenter Server que as VMs que pretende detetar estão em pastas, datacenters, clusters ou anfitriões que suportem a limitação de 1500 máquinas.
- Recomendamos que para fins de avaliação, mantenha as máquinas com as interdependências dentro do mesmo projeto e avaliação. No vCenter Server, certifique-se de que as máquinas dependentes estão na mesma pasta, datacenter ou cluster para a avaliação.

Dependendo do seu cenário, pode dividir suas descobertas conforme prescrito abaixo:

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>VCenter vários servidores com menos de 1500 VMs
Se tiver vários servidores de vCenter no seu ambiente e o número total de máquinas virtuais é inferior a 1500, pode usar a seguinte abordagem com base no seu cenário:

**Deteção contínua:** Em caso de deteção contínua, uma aplicação pode ser ligada apenas um único projeto. Portanto, precisa implementar uma aplicação para cada um dos seus servidores vCenter e, em seguida, criar um projeto para cada aplicação e deteções de Acionador em conformidade.

**Deteção única (agora preterida):** Pode utilizar um único recoletor e um projeto de migração única para detetar todas as máquinas virtuais em todos os servidores vCenter. Uma vez que o recoletor de uso individual de deteção Deteta um vCenter Server ao mesmo tempo, pode executar o recoletor mesmo em relação a todos o servidores vCenter, um após o outro e apontar o recoletor para o mesmo projeto de migração. Depois que todas as deteções forem concluídas, em seguida, pode criar avaliações para as máquinas.


### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>VCenter vários servidores com mais de 1500 VMs

Se tiver vários servidores de vCenter com menos de 1500 máquinas de virtuais por servidor vCenter, mas mais de 1500 VMs em todos os servidores vCenter, terá de criar vários projetos de migração (um projeto de migração pode conter apenas 1500 VMs). Pode conseguir isto ao criar um projeto de migração por servidor vCenter e dividir as descobertas.

**Deteção contínua:** Terá de criar várias aplicações de recoletor (um para cada servidor do vCenter) e ligar cada dispositivo de uma deteção de projeto e acionador em conformidade.

**Deteção única (agora preterida):** Pode usar um único recoletor para detetar cada servidor vCenter (um após o outro). Se pretender que as deteções para iniciar ao mesmo tempo, também pode implementar várias aplicações e executar as descobertas em paralelo.

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>Mais de 1500 máquinas num único servidor do vCenter

Se tiver mais de 1500 máquinas de virtuais num único servidor do vCenter, terá de dividir a deteção em vários projetos de migração. Dividir deteções, pode tirar partido do campo de âmbito a aplicação e especifique o anfitrião, cluster, pasta ou centro de dados que pretende detetar. Por exemplo, se tiver duas pastas no vCenter Server, um com 1000 VMs (Pasta1) e outro com 800 VMs (Folder2), pode utilizar o campo de âmbito para dividir as deteções entre essas pastas.

**Deteção contínua:** Neste caso, terá de criar duas aplicações de recoletor, para o recoletor primeiro, especifique o âmbito como Pasta1 e ligá-la para o primeiro projeto de migração. É possível em paralelo começar a deteção do Folder2 com a aplicação recoletora segundo e ligá-lo para o segundo projeto de migração.

**Deteção única (agora preterida):** Pode utilizar o mesmo recoletor para acionar a ambas as descobertas. Na primeira deteção, pode especificar Pasta1 como o âmbito e apontá-lo para o primeiro projeto de migração, assim que a primeira deteção estiver concluída, pode utilizar o mesmo coletor, altero seu escopo para detalhes do projeto Folder2 e migração para o segundo projeto de migração e Faça a deteção de segundo.

### <a name="multi-tenant-environment"></a>Ambiente de multi-inquilino

Se tiver um ambiente que é partilhado em inquilinos e não pretender detetar as VMs de um inquilino na subscrição de outro inquilino, pode utilizar o campo de âmbito em que a aplicação recoletora para definir o âmbito de deteção. Se os inquilinos estão a partilhar anfitriões, criar uma credencial que tem acesso só de leitura para apenas as VMs que pertencem ao inquilino específico e, em seguida, utilize esta credencial a aplicação recoletora e especifique o âmbito como host para fazer a deteção.

## <a name="discover-on-premises-environment"></a>Detetar o ambiente no local

Assim que estiver pronto com o seu plano, pode, em seguida, iniciar a deteção das máquinas virtuais no local:

### <a name="create-a-project"></a>Criar um projeto

Crie um projeto do Azure Migrate em conformidade com os seus requisitos:

1. No portal do Azure, selecione **Criar um recurso**.
2. Procure **Azure Migrate** e selecione o serviço **Azure Migrate** nos resultados da pesquisa. Em seguida, selecione **Criar**.
3. Especifique um nome de projeto e a subscrição do Azure para o projeto.
4. Crie um novo grupo de recursos.
5. Especifique a localização na qual pretende criar o projeto e, em seguida, selecione **criar**. Tenha em atenção que ainda pode avaliar as suas VMs para outra localização de destino. A localização especificada para o projeto é utilizada para armazenar os metadados recolhidos das VMs no local.

### <a name="set-up-the-collector-appliance"></a>Configurar a aplicação recoletora

O Azure Migrate cria uma VM no local, conhecida como aplicação recoletora. Esta VM Deteta as VMs de VMware no local e envia metadados sobre as mesmas para o serviço Azure Migrate. Para configurar a aplicação recoletora, transfira um ficheiro OVA e importe-o para a instância do vCenter Server no local.

#### <a name="download-the-collector-appliance"></a>Transferir a aplicação recoletora

Se tiver vários projetos, terá de transferir a aplicação recoletora apenas uma vez ao vCenter Server. Depois de transferir e configurar a aplicação, executá-lo para cada projeto e especifique o ID exclusivo do projeto e a chave.

1. No projeto do Azure Migrate, clique em **Começar** > **Detetar e Avaliar** > **Detetar Máquinas**.
2. Na **detetar máquinas**, clique em **transferir** para transferir a aplicação.

    A aplicação do Azure Migrate comunica com o vCenter Server e perfis continuamente o ambiente no local para recolher dados de utilização em tempo real para cada VM. Recolhe os contadores de pico para cada uma (utilização da CPU, utilização da memória etc.). Este modelo não utiliza as definições de estatísticas do vCenter Server para a recolha de dados de desempenho. Pode parar a criação contínua de perfis em qualquer altura da aplicação.

    > [!NOTE]
    > A aplicação de deteção de uso individual foi agora preterida como esse método baseou-se no vCenter definições de estatísticas do servidor para a disponibilidade de ponto de dados de desempenho e coletados contadores de desempenho médio que resultou em insuficientemente dimensionamento de VMs para a migração para o Azure.

    **Resultados instantâneos:** Com a aplicação da deteção contínua, assim que estiver a deteção concluir (demora duas horas, consoante o número de VMs), pode criar avaliações de imediato. Uma vez que a recolha de dados de desempenho é iniciado quando pode iniciar a deteção, se estiver à procura de instantâneos, selecione o critério de tamanho na avaliação como *como no local*. Para obter avaliações baseado no desempenho, é recomendado aguardar pelo menos um dia após iniciar deteção para obter recomendações de tamanho fiável.

    Tenha em atenção que a aplicação recolhe apenas dados de desempenho continuamente, não deteta qualquer alteração de configuração no ambiente no local (ou seja, adição de VM, eliminação, adição de disco, etc.). Se houver uma alteração de configuração no ambiente no local, pode fazer o seguinte para refletir as alterações no portal:

    - Adição de itens (VMs, discos, núcleos, etc.): Para refletir estas alterações no portal do Azure, pode parar a deteção a partir da aplicação e, em seguida, inicie-o novamente. Isto irá garantir que as alterações são atualizadas no projeto do Azure Migrate.

    - Eliminação das VMs: A forma como a aplicação foi concebida, a eliminação de VMs não será refletida, mesmo se parar e iniciar a deteção. Isto acontece porque os dados das deteções subsequentes são anexados às deteções mais antigas e não são substituídos. Neste caso, pode simplesmente ignorar a VM no portal, ao removê-la do seu grupo e recalcular a avaliação.

3. Na **copiar as credenciais do projeto**, copie o ID e a chave para o projeto. Precisará destes dados quando configurar o recoletor.


#### <a name="verify-the-collector-appliance"></a>Verificar a aplicação recoletora

Verifique que o ficheiro OVA é seguro antes de a implementar:

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.

2. Execute o comando abaixo para gerar o hash para o OVA:

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Certifique-se de que o hash gerado corresponde às seguintes definições.

#### <a name="continuous-discovery"></a>Deteção contínua

Para a versão OVA 1.0.10.4

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be

#### <a name="one-time-discovery-deprecated-now"></a>Deteção única (agora preterida)

Para a versão ova 1.0.9.15 (lançamento 10/23/2018)

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

Para a versão ova 1.0.9.14 (lançamento 8/24/2018)

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

Para a versão OVA 1.0.9.8

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | b5d9f0caf15ca357ac0563468c2e6251
SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff

Para a versão OVA 1.0.9.7

**Algoritmo** | **Valor de hash**
--- | ---
MD5 | d5b6a03701203ff556fa78694d6d7c35
SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

### <a name="create-the-collector-vm"></a>Criar a VM do recoletor

Importe o ficheiro transferido para o servidor vCenter:

1. Na consola do vSphere Client, selecione **arquivo** > **implementar o modelo OVF**.

    ![Implementar OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. No Assistente de implementação de modelo OVF > **origem**, especifique a localização do ficheiro OVA.
3. Em **Nome** e **Localização**, especifique um nome amigável para a VM do recoletor e o objeto de inventário em que será alojada a VM.
4. Em **Anfitrião/Cluster**, especifique o anfitrião ou cluster em que a VM do recoletor será executada.
5. No armazenamento, especifique o destino de armazenamento para a VM do recoletor.
6. Em **Formato do Disco**, especifique o tipo e o tamanho do disco.
7. Em **Mapeamento da Rede**, especifique a rede à qual se ligará a VM do recoletor. A rede necessita de conectividade com a internet para enviar metadados para o Azure.
8. Reveja e confirme as definições e, em seguida, selecione **concluir**.

### <a name="identify-the-id-and-key-for-each-project"></a>Identificar o ID e a chave para cada projeto

Se tiver vários projetos, certifique-se de que identificar o ID e chave para cada um deles. Precisa da chave quando executar o recoletor para detetar as VMs.

1. No projeto, selecione **introdução** > **detetar e avaliar** > **detetar máquinas**.
2. Na **copiar as credenciais do projeto**, copie o ID e a chave para o projeto.
    ![Copiar as credenciais do projeto](./media/how-to-scale-assessment/copy-project-credentials.png)

### <a name="run-the-collector-to-discover-vms"></a>Executar o recoletor para detetar VMs

Para cada deteção tiver de realizar, execute o recoletor para detetar VMs no âmbito necessário. Execute as deteções um após o outro. Deteções simultâneas não são suportadas e cada deteção tem de ter um âmbito diferente.

1.  Na consola do vSphere Client, clique com o botão direito do rato na VM > **Abrir Consola**.
2.  Forneça as preferências de idioma, fuso horário e palavra-passe para a aplicação.
3.  Na área de trabalho, selecione o **executar recoletor** atalho.
4.  No recoletor do Azure Migrate, abra **configurar pré-requisitos** e, em seguida:

    a. Aceite os termos de licenciamento e leia as informações de terceiros.

    O recoletor verifica se a VM tem acesso à Internet.

    b. Se a VM acede à internet através de um proxy, selecione **definições de Proxy**e especifique o endereço de proxy e porta de escuta. Especifique as credenciais se o proxy precisar de autenticação.

    O recoletor verifica se o serviço do recoletor está em execução. O serviço está instalado por predefinição na VM do recoletor.

    c. Transfira e instale o VMware PowerCLI.

5.  Em **Especificar detalhes do vCenter Server**, efetue o seguinte:
    - Especifique o nome (FQDN) ou o endereço IP do vCenter Server.
    - Na **nome de utilizador** e **palavra-passe**, especifique as credenciais de conta só de leitura que o recoletor utilizará para detetar VMs no vCenter Server.
    - Em **Select scope** (Selecionar âmbito), selecione um âmbito para a deteção de VMs. O coletor pode detetar apenas as VMs dentro do âmbito especificado. O âmbito pode ser definido para uma pasta, datacenter ou cluster específicos. Não deve conter mais de 1000 VMs.

6.  Na **especificar projeto de migração**, especifique o ID e a chave para o projeto. Se não o tiver copiado-las, abra o portal do Azure da VM do recoletor. Do projeto **descrição geral** página, selecione **detetar máquinas** e copie os valores.  
7.  Na **ver o progresso da coleção**, monitorizar o processo de deteção e verifique se os metadados recolhidos das VMs estão no âmbito. O recoletor fornece um período de deteção aproximado.

#### <a name="verify-vms-in-the-portal"></a>Verificar as VMs no portal

O recoletor irá de perfil continuamente o ambiente no local e irá enviar os dados de desempenho num intervalo de hora. Pode consultar as máquinas no portal uma hora após o início da deteção. É vivamente recomendado aguardar pelo menos um dia antes de criar qualquer avaliação das VMs baseada no desempenho.

1. No projeto de migração, clique em **Gerir** > **Máquinas**.
2. Verifique se as VMs que quer detetar aparecem no portal.

### <a name="data-collected-from-on-premises-environment"></a>Dados recolhidos a partir do ambiente no local

A aplicação recoletora Deteta os seguintes dados de configuração sobre as máquinas virtuais selecionadas.

1. Nome a apresentar da VM (no vCenter)
2. Caminho de inventário da VM (anfitrião/pasta no vCenter)
3. Endereço IP
4. Endereço MAC
5. Sistema operativo
5. Número de núcleos, discos, NICs
6. Tamanho da memória, tamanhos de disco
7. E contadores de desempenho da VM, disco e rede, tal como indicado na tabela abaixo.

A aplicação recoletora recolhe os seguintes contadores de desempenho para cada VM do anfitrião ESXi num intervalo de 20 segundos. Esses contadores são contadores do vCenter e embora a terminologia diga média, os exemplos de 20 segundos são contadores em tempo real. A aplicação, em seguida, rolls-up os exemplos de 20 segundos para criar um ponto de dados individual para cada 15 minutos, selecionando o valor de pico de exemplos de 20 segundos e envia-os para o Azure. Os dados de desempenho para as VMs começaram a se tornar disponível no portal de duas horas depois de ter iniciada a deteção. Recomenda-se vivamente que aguarde, pelo menos, um dia antes de criar com base em desempenho avaliações para obter recomendações de tamanho adequado precisas. Se estiver à procura de instantâneos, pode criar avaliações com critério de dimensionamento como *como no local* qual não irá considerar os dados de desempenho para o dimensionamento certo.

**Contador** |  **Impacto na avaliação**
--- | ---
cpu.usage.average | Tamanho VM recomendados e os custos  
mem.usage.average | Tamanho VM recomendados e os custos  
virtualDisk.read.average | Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
virtualDisk.write.average | Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
virtualDisk.numberReadAveraged.average | Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
virtualDisk.numberWriteAveraged.average | Calcula o tamanho do disco, o custo de armazenamento, o tamanho da VM
net.received.average | Calcula o tamanho da VM                          
net.transmitted.average | Calcula o tamanho da VM     

> [!WARNING]
> O método de deteção única que contavam com definições de estatística do vCenter Server para a recolha de dados de desempenho foi agora preterido.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um grupo](how-to-create-a-group.md) para avaliação.
- [Saiba mais](concepts-assessment-calculation.md) sobre como são calculadas as avaliações.
