---
title: Tutorial para encomendar Caixa de Dados Azure / Microsoft Docs
description: Neste tutorial, conheça a Azure Data Box, uma solução híbrida que permite importar dados no local para o Azure e como encomendar a Azure Data Box.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: alkohli
ms.openlocfilehash: fd165795be85c26cdfcaee3c4fd01427274a7316
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210346"
---
# <a name="tutorial-order-azure-data-box"></a>Tutorial: Encomendar o Azure Data Box

O Azure Data Box é uma solução híbrida que permite importar os dados no local para o Azure, de forma rápida, fácil e fiável. Transfira os seus dados para um dispositivo de armazenamento 80-TB (capacidade utilizável) fornecido pela Microsoft e, em seguida, envie o dispositivo de volta. Estes dados são então carregados para o Azure.

Este tutorial descreve como pode encomendar um Azure Data Box. Neste tutorial, ficará a saber mais sobre:  

> [!div class="checklist"]
>
> * Pré-requisitos para implementar o Data Box
> * Encomendar um Data Box
> * Controlar a encomenda
> * Cancelar a encomenda

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/portal)

Preencha os seguintes pré-requisitos de configuração para o serviço de Caixa de Dados e dispositivo antes de implementar o dispositivo:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Pode entrar no Azure e executar os comandos Azure CLI de uma de duas maneiras:

* Pode instalar o CLI e executar os comandos CLI localmente.
* Pode executar comandos CLI a partir do portal Azure, em Azure Cloud Shell.

Utilizamos o Azure CLI através do Windows PowerShell para o tutorial, mas é livre de escolher qualquer uma das opções.

### <a name="for-azure-cli"></a>Para Azure CLI

Antes de começar, certifique-se de que:

#### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

* Instale a versão [Azure CLI](/cli/azure/install-azure-cli) 2.0.67 ou posterior. Em alternativa, pode [instalar-se utilizando o MSI.](https://aka.ms/installazurecliwindows)

**Iniciar sessão no Azure**

Abra uma janela de comando Windows PowerShell e inicie sessão no Azure com o comando [de login az:](/cli/azure/reference-index#az-login)

```azurecli
PS C:\Windows> az login
```

Aqui está a saída de um sucesso de sindesção:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

**Instale a extensão CLI da Caixa de Dados Azure**

Antes de poder utilizar os comandos CLI da Caixa de Dados Azure, tem de instalar a extensão. As extensões da CLI do Azure dão-lhe acesso a comandos experimentais e de pré-lançamento que ainda não foram enviados como parte do núcleo da CLI. Para obter mais informações sobre as extensões, veja [Utilizar extensões com o CLI do Azure](/cli/azure/azure-cli-extensions-overview).

Para instalar a extensão para Azure Data Box, execute o seguinte `az extension add --name databox` comando:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Se a extensão for instalada com sucesso, verá a seguinte saída:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

#### <a name="use-azure-cloud-shell"></a>Utilizar o Azure Cloud Shell

Você pode usar [Azure Cloud Shell](https://shell.azure.com/), um ambiente de concha interativa azure hospedado, através do seu navegador para executar comandos CLI. A Azure Cloud Shell suporta os serviços Bash ou Windows PowerShell com os serviços Azure. O Azure CLI está pré-instalado e configurado para ser utilizado com a sua conta. Selecione o botão Cloud Shell no menu na secção superior direita do portal Azure:

![Seleção de menus Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

O botão lança uma concha interativa que pode usar para executar os passos descritos neste artigo de como fazer.

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>Para Azure PowerShell

Antes de começar, certifique-se de que:

* Instale o Windows PowerShell 6.2.4 ou superior.
* Instale o módulo Azure PowerShell (AZ).
* Instale o módulo Azure Data Box (Az.DataBox).
* Inicie sessão no Azure.

#### <a name="install-azure-powershell-and-modules-locally"></a>Instale a Azure PowerShell e módulos localmente

**Instalar ou atualizar o Windows PowerShell**

Terá de ter a versão 6.2.4 do Windows PowerShell ou superior. Para saber que versão do PowerShell instalou, corra: `$PSVersionTable` .

Verá a saída seguinte:

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

Se a sua versão for inferior a 6.2.4, tem de atualizar a sua versão do Windows PowerShell. Para instalar a versão mais recente do Windows PowerShell, consulte [instalar a Azure PowerShell](/powershell/scripting/install/installing-powershell?view=powershell-7&preserve-view=true).

**Instalar módulos Azure PowerShell e Data Box**

Terá de instalar os módulos Azure PowerShell para utilizar o Azure PowerShell para encomendar uma Caixa de Dados Azure. Para instalar os módulos Azure PowerShell:

1. Instale o [módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).
2. Em seguida, instale a Az.DataBox utilizando o comando `Install-Module -Name Az.DataBox` .

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra uma janela de comando Windows PowerShell e inscreva-se no Azure com o comando [Connect-AzAccount:](/powershell/module/az.accounts/Connect-AzAccount)

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

Aqui está a saída de um sucesso de sindesção:

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Para obter informações detalhadas sobre como iniciar seduca no Azure usando o Windows PowerShell, consulte [Iniciar sôs-in com a Azure PowerShell](/powershell/azure/authenticate-azureps).

---

## <a name="order-data-box"></a>Encomendar o Data Box

# <a name="portal"></a>[Portal](#tab/portal)

Faça os seguintes passos no portal Azure para encomendar um dispositivo.

1. Utilize as suas credenciais do Microsoft Azure para iniciar sessão neste URL: [https://portal.azure.com](https://portal.azure.com).
2. Selecione **+ Criar um recurso** e procure *Azure Data Box*. Selecione **Azure Data Box**.

   ![Screenshot de nova secção com Caixa de Dados Azure no campo de pesquisa](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. Selecione **Criar**.

   ![Screenshot da secção Azure Data Box com opção Criar chamado](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. Verifique se o serviço Data Box está disponível na sua região. Introduza ou selecione as seguintes informações e selecione **Aplicar**.

    |Definição  |Valor  |
    |---------|---------|
    |Tipo de transferência     | Selecione **Importar para o Azure**.        |
    |Subscrição     | Selecione uma subscrição EA, CSP ou Azure Sponsorship para o serviço Data Box. <br> A subscrição está ligada à sua conta de faturação.       |
    |Grupo de Recursos | Selecione um grupo de recursos existente. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. |
    |País/região de origem    |    Selecione o país/região onde os dados residem atualmente.         |
    |Região do Azure de destino     |     Selecione a região do Azure para onde pretende transferir os dados. <br> Para obter mais informações, aceda a [Disponibilidade de região](data-box-overview.md#region-availability).            |

    [![Iniciar uma ordem de importação da Caixa de Dados Azure](media/data-box-deploy-ordered/select-data-box-import-04-b.png#lightbox)

5. Selecione **Data Box**. A capacidade máxima utilizável para uma única encomenda é de 80 TB. Pode criar várias encomendas para tamanhos de dados maiores.

    ![Tamanhos de dados disponíveis: Disco de caixa de dados, 40 terabytes; Caixa de Dados, 100 terabytes; Caixa de Dados Pesada, 1000 terabytes; Envie os seus próprios discos, 1 terabyte](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. Em **Ordem,** vá ao separador **Básico.** Introduza ou selecione as seguintes informações e selecione **Seguinte: Destino de dados>**.

    |Definição  |Valor  |
    |---------|---------|
    |Subscrição      | A subscrição é automaticamente povoada com base na sua seleção anterior.|
    |Grupo de recursos    | O grupo de recursos que selecionou anteriormente. |
    |Nome da ordem de importação | Forneça um nome amigável para controlar a encomenda. <br> O nome pode ter entre 3 e 24 carateres que podem ser letras, números e hífenes. <br> O nome tem de começar e terminar com uma letra ou um número.    |

    ![Data Box import Assistente de encomenda, ecrã básico, com informação correta preenchida](media/data-box-deploy-ordered/select-data-box-import-06.png)

7. No ecrã de **destino data,** selecione o **destino Data** - contas de armazenamento ou discos geridos.

    Se utilizar **a conta de armazenamento** como destino de armazenamento, consulte o seguinte ecrã:

    ![Data Box importe assistente de encomenda, ecrã de destino de dados, com contas de armazenamento selecionadas](media/data-box-deploy-ordered/select-data-box-import-07.png)

    Com base na região de Azure especificada, selecione uma ou mais contas de armazenamento da lista filtrada das contas de armazenamento existentes. O Data Box pode ser associado a um máximo de 10 contas de armazenamento. Também pode criar uma nova conta de **Fins gerais v1**, **Fins gerais v2** ou **Conta de armazenamento de blobs**.

   > [!NOTE]
   > - Se selecionar as contas Azure Premium FileStorage, a quota provisida na parte da conta de armazenamento aumentará para o tamanho dos dados que estão a ser copiados para as ações do ficheiro. Depois de a quota ser aumentada, não é ajustada novamente, por exemplo, se por alguma razão a Caixa de Dados não puder copiar os seus dados.
   > - Esta quota é utilizada para faturação. Depois de os seus dados forem enviados para o datacenter, deverá ajustar a quota para satisfazer as suas necessidades. Para obter mais informações, consulte [a faturação de Compreensão.](../../articles/storage/files/understanding-billing.md)

    As contas de armazenamento com redes virtuais são suportadas. Para permitir que o serviço Data Box funcione com contas de armazenamento protegidas, ative os serviços fidedignos nas definições de firewall de rede da conta de armazenamento. Para mais informações, consulte como [adicionar a Azure Data Box como um serviço de confiança](../storage/common/storage-network-security.md#exceptions).

    Se utilizar a Caixa de Dados para criar **discos geridos** a partir dos discos rígidos virtuais (VHDs) no local, também terá de fornecer as seguintes informações:

    |Definição  |Valor  |
    |---------|---------|
    |Grupos de recursos     | Crie novos grupos de recursos se quiser criar discos geridos a partir dos VHDs no local. Só pode utilizar um grupo de recursos existente se o grupo de recursos tiver sido criado anteriormente ao criar uma encomenda de Caixa de Dados para discos geridos pelo serviço Data Box. <br> Especifique vários grupos de recursos separados por ponto e vírgula. É suportado um máximo de 10 grupos de recursos.|

    ![Assistente de encomenda de importação de caixa de dados, ecrã de destino de dados, com Discos Geridos selecionados](media/data-box-deploy-ordered/select-data-box-import-07-b.png)

    A conta de armazenamento especificada para os discos geridos é utilizada como uma conta de armazenamento de teste. O serviço Data Box carrega os VHDs como blobs de páginas na conta de armazenamento de teste antes de os converter em discos geridos e de os mover para os grupos de recursos. Para obter mais informações, veja [Verificar o carregamento de dados no Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

   > [!NOTE]
   > Se uma bolha de página não for convertida com sucesso para um disco gerido, fica na conta de armazenamento e é cobrado para armazenamento.

8. Selecione **Seguinte: Segurança** para continuar.

    O ecrã **de Segurança** permite-lhe usar a sua própria chave de encriptação e o seu próprio dispositivo e partilhar senhas, e optar por usar encriptação dupla.

    Todas as definições no ecrã **de Segurança** são opcionais. Se não alterar quaisquer definições, aplicar-se-ão as definições predefinidas.

    ![Tela de segurança do assistente de encomenda de importação de caixa de dados](media/data-box-deploy-ordered/select-data-box-import-security-01.png)

9. Se pretender utilizar a sua própria chave gerida pelo cliente para proteger a chave de desbloqueio para o seu novo recurso, expanda o **tipo de encriptação**.

    Configurar uma chave gerida pelo cliente para a sua Caixa de Dados Azure é opcional. Por predefinição, a Data Box utiliza uma chave gerida pela Microsoft para proteger a chave de desbloqueio.

    Uma chave gerida pelo cliente não afeta a forma como os dados do dispositivo são encriptados. A chave é usada apenas para encriptar a chave de desbloqueio do dispositivo.

    Se não quiser utilizar uma chave gerida pelo cliente, salte para o passo 15.

   ![Ecrã de segurança mostrando definições do tipo de encriptação](./media/data-box-deploy-ordered/customer-managed-key-01.png)

10. Selecione **a chave gerida pelo Cliente** como o tipo chave. Em seguida, **selecione selecione um cofre e uma chave de teclas**.
   
    ![Ecrã de segurança, definições para uma chave gerida pelo cliente](./media/data-box-deploy-ordered/customer-managed-key-02.png)

11. Na **tecla Select da lâmina Azure Key Vault,** a subscrição é automaticamente povoada.

    - Para **o cofre key,** pode selecionar um cofre de chaves existente na lista de dropdown.

      ![Selecione a chave do ecrã Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-03.png)

    - Também pode selecionar **Criar novo** para criar um novo cofre de chaves. No ecrã do **cofre da chave Create,** insira o grupo de recursos e um nome de cofre chave. Certifique-se de que a proteção **para eliminar** e **purgar** suavemente está ativada. Aceite todos os outros incumprimentos e selecione **Review + Create**.

      ![Criar uma nova definição de Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-04.png)

      Reveja as informações para o seu cofre chave e selecione **Criar**. Espere alguns minutos para a criação do cofre para completar.

      ![Novo ecrã de revisão do Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-05.png)

12. Na **tecla Select from Azure Key Vault,** pode selecionar uma chave existente no cofre da chave.

    ![Selecione a chave existente a partir do Cofre de Chaves Azure](./media/data-box-deploy-ordered/customer-managed-key-06.png)

    Se pretender criar uma nova chave, **selecione Criar nova**. Tem de usar uma chave RSA. O tamanho pode ser 2048 ou maior. Introduza um nome para a sua nova tecla, aceite as outras predefinições e selecione **Criar**.

      ![Criar uma nova opção chave](./media/data-box-deploy-ordered/customer-managed-key-07.png)

      Será notificado quando a chave for criada no seu cofre.

13. Selecione a **versão** da chave a utilizar e, em seguida, escolha **Selecione**.

      ![Nova chave criada no cofre chave](./media/data-box-deploy-ordered/customer-managed-key-08.png)

    Se pretender criar uma nova versão chave, selecione **Criar novo**.

    ![Abra uma caixa de diálogo para criar uma nova versão chave](./media/data-box-deploy-ordered/customer-managed-key-08-a.png)

    Escolha as definições para a nova versão chave e selecione **Criar**.

    ![Criar uma nova versão chave](./media/data-box-deploy-ordered/customer-managed-key-08-b.png)

    As definições **do tipo encriptação** no ecrã **de Segurança** mostram o cofre e a chave da chave.

    ![Chave e cofre chave para uma chave gerida pelo cliente](./media/data-box-deploy-ordered/customer-managed-key-09.png)

14. Selecione uma identidade de utilizador que utilizará para gerir o acesso a este recurso. Escolha **Selecionar uma identidade de utilizador.** No painel à direita, selecione a subscrição e a identidade gerida para utilizar. Em seguida, escolha **Selecionar**.

    Uma identidade gerida atribuída pelo utilizador é um recurso autónomo do Azure que pode ser usado para gerir múltiplos recursos. Para obter mais informações, consulte [os tipos de identidade geridos.](/azure/active-directory/managed-identities-azure-resources/overview)  

    Se precisar de criar uma nova identidade gerida, siga as orientações em [Criar, listar, excluir ou atribuir uma função a uma identidade gerida atribuída pelo utilizador utilizando o portal Azure](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal).
    
    ![Selecione uma identidade de utilizador](./media/data-box-deploy-ordered/customer-managed-key-10.png)

    A identidade do utilizador é mostrada nas definições **do tipo encriptação.**

    ![Uma identidade de utilizador selecionada mostrada nas definições do tipo de encriptação](./media/data-box-deploy-ordered/customer-managed-key-11.png)

15. Se não quiser utilizar as palavras-passe geradas pelo sistema que a Azure Data Box utiliza por padrão, **expanda a sua própria palavra-passe** no ecrã **de Segurança.**

    As palavras-passe geradas pelo sistema são seguras e são recomendadas a menos que a sua organização exija o contrário.

    ![Expandido Traga as suas próprias opções de senha para uma encomenda de importação de Caixa de Dados](media/data-box-deploy-ordered/select-data-box-import-security-02.png) 

   - Para utilizar a sua própria palavra-passe para o seu novo dispositivo, por **definição de preferência para a palavra-passe do dispositivo**, selecione **Use a sua própria palavra-passe** e escreva uma palavra-passe que satisfaça os requisitos de segurança.
   
     ![Opções para usar a palavra-passe do seu próprio dispositivo no ecrã de Segurança para uma ordem de importação de Caixa de Dados](media/data-box-deploy-ordered/select-data-box-import-security-03.png)

 - Para utilizar as suas próprias palavras-passe para ações:

   - Por **Definir a preferência por palavras-passe de partilha**, selecione Use as suas **próprias palavras-passe** e, em seguida, **selecione palavras-passe para as ações**.
     
        ![Opções para usar as suas próprias palavras-passe no ecrã de Segurança para uma encomenda de importação de Caixa de Dados](media/data-box-deploy-ordered/select-data-box-import-security-04.png)

    - Digite uma palavra-passe para cada conta de armazenamento na encomenda. A palavra-passe será usada em todas as ações para a conta de armazenamento.
     
        Para utilizar a mesma palavra-passe para todas as contas de armazenamento, selecione **Copy to all**. Quando terminar, **selecione Save**.
     
        ![Tela para introduzir palavras-passe de partilha para uma encomenda de importação de Caixa de Dados](media/data-box-deploy-ordered/select-data-box-import-security-05.png)

       No ecrã **'Segurança',** pode utilizar **ver ou alterar palavras-passe** para alterar as palavras-passe.

16. Em **Segurança**, se pretender ativar a dupla encriptação baseada em software, expanda a **encriptação dupla (para ambientes altamente seguros)** e selecione **Ative a dupla encriptação para a encomenda**.

    ![Ecrã de segurança para importação de Data Box, permitindo encriptação baseada em software para uma encomenda de Caixa de Dados](media/data-box-deploy-ordered/select-data-box-import-security-07.png)

    A encriptação baseada em software é realizada para além da encriptação bit AES-256 dos dados na Caixa de Dados.

    > [!NOTE]
    > Permitir esta opção poderia fazer com que o processamento de encomendas e a cópia de dados demorasse mais tempo. Não pode alterar esta opção depois de criar o seu pedido.

    Selecione **Seguinte: Contacte os dados** para continuar.

17. Em **detalhes de contato**, selecione **+ Adicionar endereço de envio**.

    ![A partir do ecrã de detalhes de contato, adicione endereços de envio à sua encomenda de importação da Caixa de Dados Azure](media/data-box-deploy-ordered/select-data-box-import-08-a.png)

18. No separador **Endereço para envio**, forneça o seu nome próprio e apelido, o nome e o endereço postal da empresa e um número de telefone válido. Selecione **Validar endereço**. O serviço valida o endereço de envio relativamente à disponibilidade do serviço. Se o serviço estiver disponível para o endereço de envio especificado, receberá uma notificação para o efeito.

    ![Screenshot da caixa de diálogo do endereço de envio de envio com o Navio usando opções e a opção de endereço de envio Adicionar chamada.](media/data-box-deploy-ordered/select-data-box-import-10.png)

    Se selecionar o envio auto-gerido, receberá uma notificação de e-mail após a encomenda ser feita com sucesso. Para obter mais informações sobre o transporte auto-gerido, consulte [use o envio auto-gerido](data-box-portal-customer-managed-shipping.md).

19. Selecione **Adicionar Endereço de Envio** assim que os dados de envio tenham sido validados com sucesso. Regressará ao separador **Dados de Contacto.**

20. Depois de voltar aos **dados de Contato,** adicione um ou mais endereços de e-mail. O serviço envia notificações por e-mail relativamente a todas as atualizações do estado da encomenda para os endereços de e-mail especificados.

    Recomendamos que utilize um e-mail de grupo para continuar a receber notificações se um administrador sair do grupo.

    ![Secção de email de dados de contacto no assistente da Ordem](media/data-box-deploy-ordered/select-data-box-import-08-c.png)

21. Reveja as informações em **Review + Order** relacionadas com os termos de encomenda, contacto, notificação e privacidade. Selecione a caixa correspondente ao contrato de termos de privacidade.

22. Selecione **Encomendar**. A encomenda demora alguns minutos a ser criada.

    ![Rever e encomendar tela do assistente da Ordem](media/data-box-deploy-ordered/select-data-box-import-11.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Faça os seguintes passos utilizando o Azure CLI para encomendar um dispositivo:

1. Anote as suas definições para a sua encomenda de Caixa de Dados. Estas definições incluem as suas informações pessoais/empresariais, nome de subscrição, informações do dispositivo e informações de envio. Terá de utilizar estas definições como parâmetros ao executar o comando CLI para criar a ordem Caixa de Dados. A tabela a seguir mostra as definições de parâmetros utilizadas `az databox job create` para:

   | Definição (parâmetro) | Description |  Valor da amostra |
   |---|---|---|
   |resource-group| Utilize um grupo de recursos existente ou crie um novo. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. | "grupo myresource"|
   |name| O nome da ordem que está a criar. | "mydataboxorder"|
   |nome de contacto| O nome associado ao endereço de envio. | "Gus Polónia"|
   |telefone| O número de telefone da pessoa ou empresa que receberá a encomenda.| "14255551234"
   |localização| A região de Azure mais próxima para si que irá enviar o seu dispositivo.| "Eua Oeste"|
   |sku| O dispositivo específico da Caixa de Dados que está a encomendar. Os valores válidos são: "DataBox", "DataBoxDisk" e "DataBoxHeavy"| "DataBox" |
   |lista de e-mails| Os endereços de e-mail associados à encomenda.| "gusp@contoso.com" |
   |endereço de rua1| O endereço da rua para onde a encomenda será enviada. | "15700 NE 39th St" |
   |endereço de rua2| A informação de endereço secundário, como o número de apartamento ou o número de edifício. | "Edifício 123" |
   |city| A cidade para onde o dispositivo será enviado. | "Redmond" |
   |estado ou província| O estado onde o dispositivo será enviado.| "WA" |
   |país| O país em que o dispositivo será enviado. | "Estados Unidos" |
   |código postal| O código postal ou código postal associado ao endereço de envio.| "98052"|
   |nome da empresa| O nome da sua empresa para quem trabalha.| "Contoso, LTD" |
   |conta de armazenamento| A conta de Armazenamento Azure de onde pretende importar dados.| "mystorageaccount"|
   |depurar| Incluir depurar informações para verbose logging  | --depurar |
   |Ajuda| Mostrar informações de ajuda para este comando. | --ajuda -h |
   |apenas-show-erros| Só mostre erros, suprimindo avisos. | --só-erros de exibição |
   |saída -o| Define o formato de saída.  Valores permitidos: json, jsonc, nenhum, mesa, tsv, yaml, yamlc. O valor predefinido é json. | --saída "json" |
   |consulta| A cadeia de consulta JMESPath. Para mais informações, consulte [o JMESPath.](http://jmespath.org/) | -consulta <string>|
   |verbose| Inclua a registo verboso. | -verbose |

2. No seu pedido de comando de escolha ou terminal, executar [a az data box job criar](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create&preserve-view=true) para criar a sua encomenda Azure Data Box.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Aqui está um exemplo de utilização do comando:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Aqui está a saída de executar o comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Todos os comandos Azure CLI usarão json como formato de saída por padrão, a menos que o altere. Pode alterar o formato de saída utilizando o parâmetro `--output <output-format>` global. Mudar o formato para "tabela" melhorará a legibilidade da saída.

   Aqui está o mesmo comando que acabamos de executar com um pequeno ajuste para mudar a formatação:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Aqui está a saída de executar o comando:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

Faça os seguintes passos utilizando a Azure PowerShell para encomendar um dispositivo:

1. Antes de criar a ordem de importação, precisa de obter a sua conta de armazenamento e guardar o objeto da conta de armazenamento numa variável.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Anote as suas definições para a sua encomenda de Caixa de Dados. Estas definições incluem as suas informações pessoais/empresariais, nome de subscrição, informações do dispositivo e informações de envio. Terá de utilizar estas definições como parâmetros ao executar o comando PowerShell para criar a ordem Caixa de Dados. A tabela seguinte mostra as definições de parâmetros utilizadas para [o New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob).

    | Definição (parâmetro) | Description |  Valor da amostra |
    |---|---|---|
    |Nome do Grupo de Recursos [Obrigatório]| Utilize um grupo de recursos existente. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. | "grupo myresource"|
    |Nome [Obrigatório]| O nome da ordem que está a criar. | "mydataboxorder"|
    |Nome de contacto [Obrigatório]| O nome associado ao endereço de envio. | "Gus Polónia"|
    |Número de telefone [Obrigatório]| O número de telefone da pessoa ou empresa que receberá a encomenda.| "14255551234"
    |Localização [Necessária]| A região de Azure mais próxima para si que irá enviar o seu dispositivo.| "Westus"|
    |DataBoxType [Obrigatório]| O dispositivo específico da Caixa de Dados que está a encomendar. Os valores válidos são: "DataBox", "DataBoxDisk" e "DataBoxHeavy"| "DataBox" |
    |EmailId [Obrigatório]| Os endereços de e-mail associados à encomenda.| "gusp@contoso.com" |
    |StreetAddress1 [Obrigatório]| O endereço da rua para onde a encomenda será enviada. | "15700 NE 39th St" |
    |StreetAddress2| A informação de endereço secundário, como o número de apartamento ou o número de edifício. | "Edifício 123" |
    |StreetAddress3| A informação do endereço terciário. | |
    |Cidade [Necessária]| A cidade para onde o dispositivo será enviado. | "Redmond" |
    |StateOrProvinceCode [Obrigatório]| O estado onde o dispositivo será enviado.| "WA" |
    |CountryCode [Obrigatório]| O país em que o dispositivo será enviado. | "Estados Unidos" |
    |Código Postal [Obrigatório]| O código postal ou código postal associado ao endereço de envio.| "98052"|
    |CompanyName| O nome da sua empresa para quem trabalha.| "Contoso, LTD" |
    |ArmazenamentoAccountResourceId [Obrigatório]| O ID da conta Azure Storage de onde pretende importar dados.| <AzStorageAccount>.id |

3. No seu pedido de comando de escolha ou terminal, utilize o [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob) para criar a sua encomenda Azure Data Box.

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   Aqui está a saída de executar o comando:

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
   ```

---

## <a name="track-the-order"></a>Controlar a encomenda

# <a name="portal"></a>[Portal](#tab/portal)

Depois de fazer a encomenda, pode controlar o estado da encomenda a partir do portal do Azure. Vá à sua encomenda de Caixa de Dados e, em seguida, vá ao **Overview** para ver o estado. O portal mostra a encomenda no estado **Encomendado**.

Se o dispositivo não estiver disponível, receberá uma notificação. Se o dispositivo estiver disponível, a Microsoft identifica-o para envio e prepara o envio. Durante a preparação do dispositivo, ocorrem as ações seguintes:

* São criadas Partilhas SMB para cada conta de armazenamento associada ao dispositivo.
* Para cada partilha, são geradas credenciais de acesso, como nome de utilizador e palavra-passe.
* A palavra-passe do dispositivo, que ajuda a desbloqueá-lo, também é gerada.
* O Data Box está bloqueado para impedir o acesso não autorizado ao dispositivo a qualquer momento.

Quando a preparação do dispositivo estiver concluída, o portal mostra a encomenda no estado **Processado**.

![Uma encomenda de Caixa de Dados que foi processada](media/data-box-overview/data-box-order-status-processed.png)

Em seguida, a Microsoft prepara e expede os seus dispositivos através de uma transportadora regional. Receberá um número de encomenda assim que o dispositivo for enviado. O portal mostra a encomenda no estado **Expedido**.

![Uma ordem de caixa de dados que foi despachada](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="track-a-single-order"></a>Acompanhe uma única encomenda

Para obter informações de rastreio sobre uma única encomenda existente da Caixa de Dados Azure, corra [`az databox job show`](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show&preserve-view=true) . O comando apresenta informações sobre a ordem, como, mas não se limitando a: nome, grupo de recursos, informações de rastreio, ID de subscrição, informações de contacto, tipo de envio e sku do dispositivo.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   A tabela a seguir mostra as informações do parâmetro `az databox job show` para:

   | Parâmetro | Descrição |  Valor da amostra |
   |---|---|---|
   |grupo de recursos [Obrigatório]| O nome do grupo de recursos associado à ordem. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. | "grupo myresource"|
   |nome [Obrigatório]| O nome da ordem a ser exibida. | "mydataboxorder"|
   |depurar| Incluir depurar informações para verbose logging | --depurar |
   |Ajuda| Mostrar informações de ajuda para este comando. | --ajuda -h |
   |apenas-show-erros| Só mostre erros, suprimindo avisos. | --só-erros de exibição |
   |saída -o| Define o formato de saída.  Valores permitidos: json, jsonc, nenhum, mesa, tsv, yaml, yamlc. O valor predefinido é json. | --saída "json" |
   |consulta| A cadeia de consulta JMESPath. Para mais informações, consulte [o JMESPath.](http://jmespath.org/) | -consulta <string>|
   |verbose| Inclua a registo verboso. | -verbose |

   Aqui está um exemplo do comando com formato de saída definido para "tabela":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Aqui está a saída de executar o comando:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> A ordem de lista pode ser suportada ao nível da subscrição e isso torna o grupo de recursos um parâmetro opcional (em vez de um parâmetro necessário).

### <a name="list-all-orders"></a>Listar todas as encomendas

Se tiver encomendado vários dispositivos, pode correr [`az databox job list`](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list&preserve-view=true) para ver todas as suas encomendas da Caixa de Dados Azure. O comando lista todas as ordens que pertencem a um grupo de recursos específicos. Também apresentado na saída: nome da encomenda, estado do envio, região Azure, tipo de entrega, estado da encomenda. As encomendas canceladas também estão incluídas na lista.
O comando também apresenta selos temporais de cada ordem.

```azurecli
az databox job list --resource-group <resource-group>
```

A tabela a seguir mostra as informações do parâmetro `az databox job list` para:

   | Parâmetro | Descrição |  Valor da amostra |
   |---|---|---|
   |grupo de recursos [Obrigatório]| O nome do grupo de recursos que contém as encomendas. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. | "grupo myresource"|
   |depurar| Incluir depurar informações para verbose logging | --depurar |
   |Ajuda| Mostrar informações de ajuda para este comando. | --ajuda -h |
   |apenas-show-erros| Só mostre erros, suprimindo avisos. | --só-erros de exibição |
   |saída -o| Define o formato de saída.  Valores permitidos: json, jsonc, nenhum, mesa, tsv, yaml, yamlc. O valor predefinido é json. | --saída "json" |
   |consulta| A cadeia de consulta JMESPath. Para mais informações, consulte [o JMESPath.](http://jmespath.org/) | -consulta <string>|
   |verbose| Inclua a registo verboso. | -verbose |

   Aqui está um exemplo do comando com formato de saída definido para "tabela":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Aqui está a saída de executar o comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>Acompanhe uma única encomenda

Para obter informações de rastreio sobre uma única encomenda existente da Caixa de Dados Azure, execute [Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob). O comando apresenta informações sobre a ordem, como, mas não se limitando a: nome, grupo de recursos, informações de rastreio, ID de subscrição, informações de contacto, tipo de envio e sku do dispositivo.

> [!NOTE]
> `Get-AzDataBoxJob` é utilizado para exibir encomendas individuais e múltiplas. A diferença é que você especifica o nome da encomenda para encomendas individuais.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   A tabela a seguir mostra as informações do parâmetro `Get-AzDataBoxJob` para:

   | Parâmetro | Descrição |  Valor da amostra |
   |---|---|---|
   |Grupo de Recursos [Obrigatório]| O nome do grupo de recursos associado à ordem. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. | "grupo myresource"|
   |Nome [Obrigatório]| O nome da ordem para obter informações para. | "mydataboxorder"|
   |ResourceId| A identificação do recurso associado à ordem. |  |

   Aqui está um exemplo do comando com saída:

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   Aqui está a saída de executar o comando:

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>Listar todas as encomendas

Se tiver encomendado vários dispositivos, pode correr [`Get-AzDataBoxJob`](/powershell/module/az.databox/Get-AzDataBoxJob) para ver todas as suas encomendas da Caixa de Dados Azure. O comando lista todas as ordens que pertencem a um grupo de recursos específicos. Também apresentado na saída: nome da encomenda, estado do envio, região Azure, tipo de entrega, estado da encomenda. As encomendas canceladas também estão incluídas na lista.
O comando também apresenta selos temporais de cada ordem.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

Eis um exemplo do comando:

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

Aqui está a saída de executar o comando:

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
PS C:\WINDOWS\system32>
```

---

## <a name="cancel-the-order"></a>Cancelar a encomenda

# <a name="portal"></a>[Portal](#tab/portal)

Para cancelar esta encomenda, no portal Azure, vá ao **Overview** e **selecione Cancelar** a partir da barra de comando.

Depois de efetuar uma encomenda, pode cancelá-la a qualquer momento antes de o estado desta ser marcado como processado.

Para eliminar uma encomenda cancelada, vá ao **Overview** e selecione **Delete** da barra de comando.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="cancel-an-order"></a>Cancelar uma encomenda

Para cancelar uma encomenda da Caixa de Dados Azure, corra [`az databox job cancel`](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel&preserve-view=true) . É-lhe exigido que especifique a sua razão para cancelar a encomenda.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   A tabela a seguir mostra as informações do parâmetro `az databox job cancel` para:

   | Parâmetro | Descrição |  Valor da amostra |
   |---|---|---|
   |grupo de recursos [Obrigatório]| O nome do grupo de recursos associado à ordem a eliminar. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. | "grupo myresource"|
   |nome [Obrigatório]| O nome da ordem a ser apagada. | "mydataboxorder"|
   |razão [Necessário]| O motivo para cancelar a encomenda. | "Inseri informações erradas e precisei de cancelar a encomenda." |
   |sim| Não pedir confirmação. | --sim (-y)| --sim-y |
   |depurar| Incluir depurar informações para verbose logging | --depurar |
   |Ajuda| Mostrar informações de ajuda para este comando. | --ajuda -h |
   |apenas-show-erros| Só mostre erros, suprimindo avisos. | --só-erros de exibição |
   |saída -o| Define o formato de saída.  Valores permitidos: json, jsonc, nenhum, mesa, tsv, yaml, yamlc. O valor predefinido é json. | --saída "json" |
   |consulta| A cadeia de consulta JMESPath. Para mais informações, consulte [o JMESPath.](http://jmespath.org/) | -consulta <string>|
   |verbose| Inclua a registo verboso. | -verbose |

   Aqui está um exemplo do comando com saída:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Aqui está a saída de executar o comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Apagar uma encomenda

Se tiver cancelado uma encomenda da Caixa de Dados Azure, pode correr [`az databox job delete`](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete&preserve-view=true) para apagar a encomenda.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   A tabela a seguir mostra as informações do parâmetro `az databox job delete` para:

   | Parâmetro | Descrição |  Valor da amostra |
   |---|---|---|
   |grupo de recursos [Obrigatório]| O nome do grupo de recursos associado à ordem a eliminar. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. | "grupo myresource"|
   |nome [Obrigatório]| O nome da ordem a ser apagada. | "mydataboxorder"|
   |subscrição| O nome ou ID (GUID) da sua assinatura Azure. | "xxxx-xxxx-xxxx-xxxx-xxxx-xxx" |
   |sim| Não pedir confirmação. | --sim (-y)| --sim-y |
   |depurar| Incluir depurar informações para verbose logging | --depurar |
   |Ajuda| Mostrar informações de ajuda para este comando. | --ajuda -h |
   |apenas-show-erros| Só mostre erros, suprimindo avisos. | --só-erros de exibição |
   |saída -o| Define o formato de saída.  Valores permitidos: json, jsonc, nenhum, mesa, tsv, yaml, yamlc. O valor predefinido é json. | --saída "json" |
   |consulta| A cadeia de consulta JMESPath. Para mais informações, consulte [o JMESPath.](http://jmespath.org/) | -consulta <string>|
   |verbose| Inclua a registo verboso. | -verbose |

Aqui está um exemplo do comando com saída:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Aqui está a saída de executar o comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>Cancelar uma encomenda

Para cancelar uma encomenda da Caixa de Dados Azure, execute [Stop-AzDataBoxJob](/powershell/module/az.databox/stop-azdataboxjob). É-lhe exigido que especifique a sua razão para cancelar a encomenda.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

A tabela a seguir mostra as informações do parâmetro `Stop-AzDataBoxJob` para:

| Parâmetro | Descrição |  Valor da amostra |
|---|---|---|
|Grupo de Recursos [Obrigatório]| O nome do grupo de recursos associado à ordem a ser cancelado. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. | "grupo myresource"|
|Nome [Obrigatório]| O nome da ordem a ser apagada. | "mydataboxorder"|
|Razão [Necessária]| O motivo para cancelar a encomenda. | "Inseri informações erradas e precisei de cancelar a encomenda." |
|Force | Força o cmdlet a funcionar sem confirmação do utilizador. | -Force |

Aqui está um exemplo do comando com saída:

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

Aqui está a saída de executar o comando:

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>Apagar uma encomenda

Se tiver cancelado uma encomenda da Caixa de Dados Azure, pode correr [`Remove-AzDataBoxJob`](/powershell/module/az.databox/remove-azdataboxjob) para apagar a encomenda.

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

A tabela a seguir mostra as informações do parâmetro `Remove-AzDataBoxJob` para:

| Parâmetro | Descrição |  Valor da amostra |
|---|---|---|
|Grupo de Recursos [Obrigatório]| O nome do grupo de recursos associado à ordem a eliminar. Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. | "grupo myresource"|
|Nome [Obrigatório]| O nome da ordem a ser apagada. | "mydataboxorder"|
|Force | Força o cmdlet a funcionar sem confirmação do utilizador. | -Force |

Aqui está um exemplo do comando com saída:

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

Aqui está a saída de executar o comando:

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu sobre artigos da Azure Data Box tais como:

> [!div class="checklist"]
>
> * Pré-requisitos para implementar o Data Box
> * Encomendar o Data Box
> * Controlar a encomenda
> * Cancelar a encomenda

Avance para o tutorial seguinte para saber como configurar o Data Box.

> [!div class="nextstepaction"]
> [Configurar o Azure Data Box](./data-box-deploy-set-up.md)
