---
title: Avaliar servidores no local utilizando um ficheiro CSV importado com avaliação do servidor Azure Migrate
description: Descreve como descobrir servidores no local para migração para Azure usando um ficheiro CSV importado na Avaliação do Servidor migratório de Azure
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: 743f18ce72e3f14fe54e0bbadff254ea03fc6278
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604228"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>Tutorial: Avaliar servidores usando um ficheiro CSV importado

Como parte da sua viagem de migração para Azure, você descobre o seu inventário no local e cargas de trabalho. 

Este tutorial mostra-lhe como avaliar as máquinas no local com a ferramenta Azure Migrate: Server Assessment, utilizando um ficheiro de valores separados de vírgula importada (CSV). 

Se utilizar um ficheiro CSV, não precisa de configurar o aparelho Azure Migrate para descobrir e avaliar servidores. Pode controlar os dados que partilha no ficheiro, e grande parte dos dados são opcionais. Este método é útil se:

- Pretende criar uma avaliação rápida e inicial antes de implementar a aplicação.
- Não pode implementar a aplicação do Azure Migrate na sua organização.
- Não pode partilhar credenciais que permitam o acesso a servidores no local.
- As restrições de segurança impedem que obtenha e envie dados recolhidos pela aplicação para o Azure.

> [!NOTE]
> Não é possível migrar servidores importados usando um ficheiro CSV.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar uma conta Azure
> * Configurar um projeto do Azure Migrate
> * Preparar um ficheiro CSV
> * Importar o ficheiro
> * Avaliar servidores

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam opções padrão sempre que possível. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Pode adicionar até 20.000 servidores num único ficheiro CSV e num projeto Azure Migrate. 
- Os nomes do sistema operativo especificados no ficheiro CSV devem conter e corresponder [aos nomes suportados](#supported-operating-system-names).


## <a name="prepare-an-azure-user-account"></a>Preparar uma conta de utilizador Azure

Para criar um projeto Azure Migrate, precisa de uma conta com:
- Permissões de colaborador ou proprietário numa subscrição do Azure.
- Permissões para registar aplicações do Azure Ative Directory.

Se acabou de criar uma conta gratuita do Azure, é o proprietário da sua subscrição. Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir as permissões da seguinte forma:

1. No portal Azure, procure por "subscrições", e em **Serviços,** **selecione Subscrições**.

    ![Caixa de pesquisa para procurar a subscrição do Azure](./media/tutorial-discover-import/search-subscription.png)

2. Na página **Subscrições,** selecione a subscrição na qual pretende criar um projeto Azure Migrate. 
3. Na subscrição, selecione **Access Control (IAM)**  >  **Verifique o acesso**.
4. No **Acesso ao Cheques,** procure na conta de utilizador relevante.
5. In **Add a role assignment**, clique em **Adicionar**.

    ![Procure uma conta de utilizador para verificar o acesso e atribuir uma função](./media/tutorial-discover-import/azure-account-access.png)

6. Na **atribuição de funções Adicionar**, selecione a função Contribuinte ou Proprietário e selecione a conta (azmigrateuser no nosso exemplo). Em seguida, clique em **Guardar**.

    ![Abre a página de atribuição de Função Adicionar para atribuir uma função à conta](./media/tutorial-discover-import/assign-role.png)

7. No portal, procure utilizadores e em **Serviços,** selecione **Utilizadores.**
8. Nas **definições do Utilizador,** verifique se os utilizadores de Ad Azure podem registar aplicações (definidas para **Sim** por predefinição).

    ![Verifique nas Definições do Utilizador que os utilizadores podem registar aplicações de Ative Directory](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Criar um projeto

Crie um novo projeto Azure Migrate se não tiver um.

1. No portal do Azure > **Todos os serviços**, procure **Azure Migrate**.
2. Em **Serviços**, selecione **Azure Migrate**.
3. Em **Visão Geral**, selecione **Criar projeto**.
5. No **projeto Create,** selecione o seu grupo de subscrição e recursos Azure. Crie um grupo de recursos se não tiver um.
6. Em **Detalhes do Projeto,** especifique o nome do projeto e a geografia em que pretende criar o projeto. Reveja geografias apoiadas para nuvens [públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais.](migrate-support-matrix.md#supported-geographies-azure-government)

   ![Caixas para nome e região do projeto](./media/tutorial-discover-import/new-project.png)

7. Selecione **Criar**.
8. Aguarde alguns minutos para que o projeto do Azure Migrate seja implementado.

A ferramenta **Azure Migrate: Server Assessment** é adicionada por defeito ao novo projeto.

![Página mostrando a ferramenta de avaliação do servidor adicionada por padrão](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>Preparar o CSV

Transfira o modelo CSV e adicione informações do servidor ao mesmo.

### <a name="download-the-template"></a>Transferir o modelo

1. Em **Objetivos de Migração** > **Servidores** > **Azure Migrate: Avaliação do Servidor**, selecione **Detetar**.
2. Em **Detetar máquinas**, selecione **Importar com CSV**.
3. Selecione **Transferir** para transferir o modelo CSV. Como alternativa, pode [transferi-lo diretamente](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Transferir o modelo CSV](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Adicionar informações do servidor

Obtenha dados do servidor e adicione-os ao ficheiro CSV.

- Para obter dados, pode exportá-los a partir de ferramentas que utiliza na gestão dos servidores no local, como o VMware vSphere ou a base de dados de gestão de configuração (CMDB).
- Para analisar os dados de exemplo, transfira o nosso [ficheiro de exemplo](https://go.microsoft.com/fwlink/?linkid=2108405).

A tabela seguinte resume os campos do ficheiro a preencher:

**Nome do campo** | **Obrigatório** | **Detalhes**
--- | --- | ---
**Nome do servidor** | Yes | Recomendamos que especifique o nome de domínio completamente qualificado (FQDN).
**Endereço IP** | No | Endereço do servidor.
**Núcleos** | Yes | Número de núcleos do processador alocados ao servidor.
**Memória** | Yes | Total de RAM, em MB, alocada ao servidor.
**Nome do SO** | Yes | Sistema operativo do servidor. <br/> Os nomes dos sistemas operativos que correspondem ou contêm os nomes [nesta](#supported-operating-system-names) lista são reconhecidos pela avaliação.
**Versão do SO** | No | Versão do sistema operativo do servidor.
**Arquitetura de SO** | No | Arquitetura de SO do servidor <br/> Os valores válidos são: x64, x86, amd64, 32 bits ou 64 bits
**Número de discos** | No | Não é necessário se forem indicados detalhes de discos individuais.
**Tamanho do disco 1**  | No | Tamanho máximo do disco, em GB.<br/>Pode adicionar detalhes de mais discos ao [adicionar colunas](#add-multiple-disks) ao modelo. Pode adicionar até oito discos.
**Operações de leitura do disco 1** | No | Operações de leitura do disco por segundo.
**Operações de escrita do disco 1** | No | Operações de escrita do disco por segundo.
**Débito de leitura do disco 1** | No | Dados lidos do disco por segundo, em MB por segundo.
**Débito de escrita do disco 1** | No | Dados escritos no disco por segundo, em MB por segundo.
**Percentagem de utilização da CPU** | No | Percentagem da CPU utilizada.
**Percentagem de utilização da memória** | No | Percentagem da RAM utilizada.
**Total de operações de leitura dos discos** | No | Operações de leitura de discos por segundo.
**Total de operações de escrita dos discos** | No | Operações de gravação de discos por segundo.
**Total de débito de leitura dos discos** | No | Dados lidos a partir do disco, em MB por segundo.
**Total de débito de escrita dos discos** | No | Dados escritos para o disco, em MB por segundo.
**Débito de Entrada na Rede** | No | Dados recebidos pelo servidor, em MB por segundo.
**Débito de Saída da Rede** | No | Dados transmitidos pelo servidor, em MB por segundo.
**Tipo de firmware** | No | Firmware do servidor. Os valores podem ser "BIOS" ou "UEFI".
**Endereço MAC**| No | Endereço MAC do servidor.


### <a name="add-operating-systems"></a>Adicionar sistemas operativos

A avaliação reconhece os nomes de sistemas operativos específicos. Qualquer nome especificado deve corresponder exatamente a uma das cadeias na [lista de nomes suportados](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Adicionar vários discos

O modelo disponibiliza campos predefinidos para o primeiro disco. Pode adicionar colunas semelhantes para até oito discos.

Por exemplo, para especificar todos os campos para um segundo disco, adicione estas colunas:

- Tamanho do disco 2
- Operações de leitura do disco 2
- Operações de escrita do disco 2
- Débito de leitura do disco 2
- Débito de escrita do disco 2


## <a name="import-the-server-information"></a>Importar informações do servidor

Depois de adicionar informações ao modelo CSV, importe o ficheiro CSV para a Avaliação do Servidor.

1. No Azure Migrate, em **Detetar máquinas**, aceda ao modelo concluído.
2. Selecione **Import** (Importar).
3. O estado da importação é mostrado.
    - Se forem apresentados avisos no estado, pode corrigi-los ou continuar sem os resolver.
    - Para melhorar a precisão da avaliação, melhore as informações do servidor conforme sugerido nos avisos.
    - Para ver e corrigir os avisos, selecione **Transferir .CSV dos detalhes dos avisos**. Esta operação transfere o CSV com os avisos incluídos. Analise os avisos e corrija os problemas conforme necessário.
    - Se forem apresentados erros no estado que fazem com que o estado da importação seja **Com falha**, tem de os corrigir para poder continuar com a importação:
        1. Transfira o CSV, que agora inclui os detalhes dos erros.
        1. Analise e resolva os erros conforme necessário. 
        1. Carregue o ficheiro modificado novamente.
4. Quando o estado da importação for **Concluído**, as informações do servidor foram importadas. Refresque se o processo de importação não parece estar completo.

## <a name="update-server-information"></a>Atualizar informações do servidor

Pode atualizar as informações de um servidor ao importar os respetivos dados novamente com o mesmo **Nome do servidor**. Não pode modificar o campo **Nome do servidor**. A eliminação de servidores não é atualmente suportada.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Para verificar se os servidores aparecem no portal do Azure após a deteção:

1. Abra o dashboard do Azure Migrate.
2. Na página **Azure Migrate – Servidores** > **Azure Migrate: Avaliação do Servidor**, selecione o ícone que mostra a contagem de **Servidores detetados**.
3. Selecione o separador **Com base na importação**.



## <a name="supported-operating-system-names"></a>Nomes de sistemas operativos suportados

Os nomes do sistema operativo fornecidos no CSV devem conter e corresponder. Se não o fizerem, não poderá avaliá-los. 

**A-H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows 10 Professional

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Criei um projeto Azure Migrate 
> * Servidores descobertos usando um ficheiro CSV importado. Agora, fazer uma avaliação para [a migração VMware VM para Azure VMs](tutorial-assess-vmware.md).
