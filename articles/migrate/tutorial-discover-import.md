---
title: Avaliar servidores no local utilizando um ficheiro CSV importado com avaliação do servidor Azure Migrate
description: Descreve como descobrir servidores no local para migração para Azure usando um ficheiro CSV importado na Avaliação do Servidor migratório de Azure
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: 743f18ce72e3f14fe54e0bbadff254ea03fc6278
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604228"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>Tutorial: Avaliar servidores usando um ficheiro CSV importado

Como parte da sua viagem de migração para Azure, você descobre o seu inventário no local e cargas de trabalho. 

Este tutorial mostra-lhe como avaliar as máquinas no local com a ferramenta Azure Migrate: Server Assessment, utilizando um ficheiro de valores separados de vírgula importada (CSV). 

Se utilizar um ficheiro CSV, não precisa de configurar o aparelho Azure Migrate para descobrir e avaliar servidores. Pode controlar os dados que partilha no ficheiro, e grande parte dos dados são opcionais. Este método é útil se:

- Pretende-se criar uma avaliação rápida e inicial antes de colocar o aparelho.
- Não pode colocar o aparelho Azure Migrate na sua organização.
- Não é possível partilhar credenciais que permitam o acesso a servidores no local.
- As restrições de segurança impedem-no de recolher e enviar dados recolhidos pelo aparelho para a Azure.

> [!NOTE]
> Não é possível migrar servidores importados usando um ficheiro CSV.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar uma conta Azure
> * Criar um projeto Azure Migrate
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

Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição. Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir as permissões da seguinte forma:

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

Descarregue o modelo CSV e adicione informações do servidor.

### <a name="download-the-template"></a>Transferir o modelo

1. In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, select **Discover**.
2. Nas **máquinas Discover**, selecione **Import using CSV**.
3. Selecione **Baixar** para descarregar o modelo CSV. Em alternativa, pode [descarregá-lo diretamente.](https://go.microsoft.com/fwlink/?linkid=2109031)

    ![Baixar o modelo CSV](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Adicionar informações do servidor

Recolha os dados do servidor e adicione-os ao ficheiro CSV.

- Para recolher dados, pode exportá-lo a partir de ferramentas que utiliza para a gestão de servidores no local, como vMware vSphere ou a sua base de dados de gestão de configuração (CMDB).
- Para rever os dados da amostra, descarregue o nosso [ficheiro de exemplo.](https://go.microsoft.com/fwlink/?linkid=2108405)

A tabela seguinte resume os campos de ficheiros a preencher:

**Nome do campo** | **Obrigatório** | **Detalhes**
--- | --- | ---
**Nome do servidor** | Yes | Recomendamos especificar o nome de domínio totalmente qualificado (FQDN).
**Endereço IP** | No | Endereço do servidor.
**Núcleos** | Yes | Número de núcleos de processador atribuídos ao servidor.
**Memória** | Yes | RAM total, em MB, alocado ao servidor.
**Nome do SO** | Yes | Sistema operativo do servidor. <br/> Os nomes do sistema operativo que correspondam ou contenham os nomes [desta](#supported-operating-system-names) lista são reconhecidos pela avaliação.
**Versão OS** | No | Versão do sistema operativo do servidor.
**Arquitetura de OS** | No | Arquitetura de SERVIDOR OS <br/> Valores válidos são: x64, x86, amd64, 32-bit ou 64-bit
**Número de discos** | No | Não é necessário se forem fornecidos detalhes individuais do disco.
**Disco 1 tamanho**  | No | Tamanho máximo do disco, em GB.<br/>Pode adicionar detalhes para mais discos [adicionando colunas](#add-multiple-disks) no modelo. Pode somar até oito discos.
**Disk 1 ler ops** | No | Operações de leitura de disco por segundo.
**Disk 1 escrever ops** | No | Operações de escrita de disco por segundo.
**Produção de leitura de disco 1** | No | Dados lidos a partir do disco por segundo, em MB por segundo.
**Saída de escrita do disco 1** | No | Dados escritos para disco por segundo, em MB por segundo.
**Percentagem de utilização do CPU** | No | Percentagem de CPU usado.
**Percentagem de utilização da memória** | No | Percentagem de RAM usado.
**Total de discos lêem ops** | No | Operações de leitura de discos por segundo.
**Os discos totais escrevem ops** | No | Operações de gravação de discos por segundo.
**Total de discos lêem produção** | No | Dados lidos a partir do disco, em MB por segundo.
**Total de discos escrevem produção** | No | Dados escritos para o disco, em MB por segundo.
**Rede Em produção** | No | Dados recebidos pelo servidor, em MB por segundo.
**Produção de Rede Para Fora** | No | Dados transmitidos pelo servidor, em MB por segundo.
**Tipo de firmware** | No | Firmware do servidor. Os valores podem ser "BIOS" ou "UEFI".
**Endereço MAC**| No | Endereço MAC do servidor.


### <a name="add-operating-systems"></a>Adicionar sistemas operativos

A avaliação reconhece nomes específicos do sistema operativo. Qualquer nome que especifique deve corresponder exatamente a uma das cordas da lista de [nomes suportados](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Adicionar vários discos

O modelo fornece campos predefinidos para o primeiro disco. Pode adicionar colunas semelhantes para até oito discos.

Por exemplo, para especificar todos os campos para um segundo disco, adicione estas colunas:

- Disco 2 tamanho
- Disk 2 ler ops
- Disk 2 escrever ops
- Produção de leitura de disco 2
- Produção de escrita de disco 2


## <a name="import-the-server-information"></a>Importar a informação do servidor

Depois de adicionar informações ao modelo CSV, importe o ficheiro CSV para a Avaliação do Servidor.

1. Em Azure Migrate, em **máquinas Discover,** vá para o modelo completo.
2. Selecione **Import** (Importar).
3. O estado de importação é indicado.
    - Se os avisos aparecerem no estado, pode corrigi-los ou continuar sem os abordar.
    - Para melhorar a precisão da avaliação, melhore as informações do servidor como sugerido nos avisos.
    - Para visualizar e corrigir avisos, **selecione Baixar os dados de aviso . CSV**. Esta operação descarrega o CSV com avisos incluídos. Reveja os avisos e corrija os problemas conforme necessário.
    - Se surgirem erros no estado de modo a que o estado de importação **seja falhado,** deve corrigir esses erros antes de poder continuar com a importação:
        1. Descarregue o CSV, que agora inclui detalhes de erro.
        1. Reveja e aborde os erros necessários. 
        1. Faça o upload do ficheiro modificado novamente.
4. Quando o estado de importação estiver **concluído,** a informação do servidor foi importada. Refresque se o processo de importação não parece estar completo.

## <a name="update-server-information"></a>Atualizar informações do servidor

Pode atualizar as informações para um servidor importando novamente os dados do servidor com o mesmo **nome de Servidor**. Não é possível modificar o campo **de nomes do Servidor.** Atualmente, a eliminação de servidores não é suportada.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Para verificar se os servidores aparecem no portal Azure após a descoberta:

1. Abra o painel Azure Migrate.
2. No **Azure Migrate - Servidores**  >  **Azure Migrate:** Página de Avaliação do servidor, selecione o ícone que exibe a contagem para **servidores descobertos**.
3. Selecione o **separador Baseado em Importação.**



## <a name="supported-operating-system-names"></a>Nomes do sistema operativo suportados

Os nomes do sistema operativo fornecidos no CSV devem conter e corresponder. Se não o fizerem, não poderá avaliá-los. 

**A-H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oráculo Linux 4/5<br/>Oráculo Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Chapéu Vermelho Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>Empresa SUSE Linux 11<br/>Empresa SUSE Linux 12<br/>Empresa SUSE Linux 8/9<br/>Empresa SUSE Linux 11<br/>SUSE abre SUSESUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Limiar do servidor do Windows<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows 10 Professional

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Criei um projeto Azure Migrate 
> * Servidores descobertos usando um ficheiro CSV importado. Agora, fazer uma avaliação para [a migração VMware VM para Azure VMs](tutorial-assess-vmware.md).
