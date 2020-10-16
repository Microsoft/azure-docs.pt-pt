---
title: Criar espaços de trabalho no portal
titleSuffix: Azure Machine Learning
description: Aprenda a criar, ver e apagar espaços de trabalho de Aprendizagem automática Azure no portal Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: d0b5d3678c3d9c7e55eede13c630510df89d5128
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045572"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Criar e gerir espaços de trabalho de aprendizagem automática Azure no portal Azure


Neste artigo, você vai criar, visualizar e eliminar [**espaços de trabalho de Aprendizagem automática Azure**](concept-workspace.md) no portal Azure para [aprendizagem automática Azure](overview-what-is-azure-ml.md).  O portal é a forma mais fácil de começar com espaços de trabalho, mas à medida que as suas necessidades mudam ou os requisitos para o aumento da automatização também pode criar e apagar espaços de trabalho [utilizando o CLI,](reference-azure-machine-learning-cli.md) [com o código Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) ou através da [extensão do Código VS.](tutorial-setup-vscode-extension.md)

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Para criar um espaço de trabalho, precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando as credenciais para a sua subscrição Azure. 

1. No canto superior esquerdo do portal Azure, selecione **+ Criar um recurso**.

      ![Criar um novo recurso](./media/how-to-manage-workspace/create-workspace.gif)

1. Utilize a barra de pesquisa para encontrar **machine learning**.

1. Selecione **Machine Learning**.

1. No painel **de aprendizagem** automática, selecione **Criar** para começar.

1. Forneça as seguintes informações para configurar o seu novo espaço de trabalho:

   Campo|Descrição 
   ---|---
   Nome da área de trabalho |Insira um nome único que identifique o seu espaço de trabalho. Neste exemplo, **usamos docs-ws**. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de recordar e diferenciar dos espaços de trabalho criados por outros. O nome do espaço de trabalho é insensível a caso.
   Subscrição |Selecione a subscrição do Azure que pretende utilizar.
   Grupo de recursos | Utilize um grupo de recursos já existente na sua subscrição ou introduza um nome para criar um grupo de recursos novo. Um grupo de recursos detém recursos relacionados para uma solução Azure. Neste exemplo, **usamos docs-aml**. Precisa de *um colaborador* ou de um *papel proprietário* para utilizar um grupo de recursos existente.  Para obter mais informações sobre o acesso, consulte [Gerir o acesso a um espaço de trabalho Azure Machine Learning.](how-to-assign-roles.md)
   Região | Selecione a região Azure mais próxima dos seus utilizadores e os recursos de dados para criar o seu espaço de trabalho.

1. Quando terminar de configurar o espaço de trabalho, selecione **Review + Create**. Opcionalmente, utilize as secções [networking](#networking) e [Advanced](#advanced) para configurar mais configurações para o espaço de trabalho.

1. Reveja as definições e escdave quaisquer alterações ou correções adicionais. Quando estiver satisfeito com as definições, **selecione Criar**.

   > [!Warning] 
   > Pode levar vários minutos para criar o seu espaço de trabalho na nuvem.

   Quando o processo está concluído, aparece uma mensagem de sucesso de implantação. 
 
 1. Para visualizar o novo espaço de trabalho, selecione **Ir para o recurso**.

### <a name="networking"></a>Redes  

> [!IMPORTANT]  
> Para obter mais informações sobre a utilização de um ponto final privado e rede virtual com o seu espaço de trabalho, consulte [o isolamento e privacidade da Rede.](how-to-enable-virtual-network.md)
    
1. A configuração de rede padrão é usar um __ponto final público__, que é acessível na internet pública. Para limitar o acesso ao seu espaço de trabalho a uma Rede Virtual Azure que criou, pode, em vez disso, selecionar __o ponto final privado__ como método de __Conectividade__, e depois utilizar __+ Adicionar__ para configurar o ponto final. 
    
   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Seleção privada de pontos finais":::  

1. No formulário __de ponto final privado Create,__ desaponte a localização, nome e rede virtual para utilizar. Se quiser utilizar o ponto final com uma Zona DNS Privada, __selecione Integre com a zona privada de DNS__ e selecione a zona utilizando o campo __Zona DNS Privada.__ Selecione __OK__ para criar o ponto final.   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Seleção privada de pontos finais":::   

1. Quando terminar de configurar a rede, pode selecionar __Review + Create__ou avançar para a configuração __avançada__ opcional. 

    > [!WARNING]    
    > Quando cria um ponto final privado, é criada uma nova Zona Privada de DNS chamada __privatelink.api.azureml.ms.__ Isto contém uma ligação com a rede virtual. Se criar vários espaços de trabalho com pontos finais privados no mesmo grupo de recursos, apenas a rede virtual para o primeiro ponto final privado pode ser adicionada à zona de DNS. Para adicionar entradas para as redes virtuais utilizadas pelos espaços de trabalho adicionais/pontos finais privados, utilize os seguintes passos: 
    >   
    > 1. No [portal Azure,](https://portal.azure.com)selecione o grupo de recursos que contém o espaço de trabalho. Em seguida, selecione o recurso Private DNS Zone denominado __privatelink.api.azureml.ms__.    
    > 2. Nas __Definições__, selecione __links de rede virtual__. 
    > 3. Selecione __Adicionar__. A partir da página __de link de rede virtual Add,__ forneça um nome __link__único e, em seguida, selecione a __rede Virtual__ a ser adicionada. Selecione __OK__ para adicionar a ligação de rede.    
    >   
    > Para obter mais informações, consulte [a configuração do DNS do Ponto Final Privado Azure](/azure/private-link/private-endpoint-dns).   

### <a name="vulnerability-scanning"></a>Digitalização de vulnerabilidades

O Centro de Segurança do Azure oferece gestão de segurança unificada e proteção contra ameaças avançada entre cargas de trabalho na cloud híbrida. Deve permitir que o Azure Security Center digitalize os seus recursos e siga as suas recomendações. Para mais informações, consulte  [a imagem do Registo de Contentores Azure pelo Security Center](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) e a [integração dos Serviços Azure Kubernetes com o Security Center](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration).

### <a name="advanced"></a>Avançado    

Por padrão, as métricas e metadados para o espaço de trabalho são armazenados num caso DB Azure Cosmos que a Microsoft mantém. Estes dados são encriptados utilizando as teclas geridas pela Microsoft.  

Para limitar os dados que a Microsoft recolhe no seu espaço de trabalho, selecione __High business impact workspace__. Para obter mais informações sobre esta definição, consulte [a encriptação em repouso](concept-enterprise-security.md#encryption-at-rest).

> [!IMPORTANT]  
> A seleção de um impacto de alto negócio só pode ser feita quando se cria um espaço de trabalho. Não é possível alterar esta definição após a criação do espaço de trabalho.   
Se estiver a utilizar a versão __Enterprise__ do Azure Machine Learning, pode, em vez disso, fornecer a sua própria chave. Ao fazê-lo, cria a instância DB do Azure Cosmos que armazena métricas e metadados na sua subscrição Azure. Use os seguintes passos para usar a sua própria chave:    

> [!IMPORTANT]  
> Antes de seguir estes passos, deve primeiro realizar as seguintes ações:   
>   
> 1. Autorize a __App de Aprendizagem automática__ (em Gestão de Identidade e Acesso) com permissões de colaboradores na sua subscrição.  
> 1. Siga os passos em [Chaves geridas pelo cliente](/azure/cosmos-db/how-to-setup-cmk) para:   
>     * Registe-se no fornecedor Azure Cosmos DB   
>     * Criar e configurar um Cofre de Chaves Azure 
>     * Gerar uma chave  
>   
>     Não precisa de criar manualmente o exemplo DB do Azure Cosmos, um deles será criado para si durante a criação do espaço de trabalho. Esta instância DB Azure Cosmos será criada num grupo de recursos separado usando um nome baseado neste padrão: `<your-workspace-resource-name>_<GUID>` .   
>   
> Não é possível alterar esta definição após a criação do espaço de trabalho. Se eliminar o Azure Cosmos DB utilizado pelo seu espaço de trabalho, também deve eliminar o espaço de trabalho que o está a utilizar.

1. Selecione __as teclas geridas pelo Cliente__e, em seguida, selecione Clique para __selecionar a tecla__.   

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Seleção privada de pontos finais":::   

1. Na __tecla Select a partir do formulário Azure Key Vault,__ selecione um Cofre de Chave Azure existente, uma chave que contém e a versão da chave. Esta chave é usada para encriptar os dados armazenados no Azure Cosmos DB. Por fim, utilize o botão __Select__ para utilizar esta tecla. 

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Seleção privada de pontos finais":::

### <a name="download-a-configuration-file"></a>Descarregue um ficheiro de configuração

1. Se estiver a criar uma [instância computacional,](tutorial-1st-experiment-sdk-setup.md#azure)ignore este passo.

1. Se pretender utilizar código no seu ambiente local que faça referência a este espaço de trabalho, selecione  **Baixar config.jsna** secção **visão geral** do espaço de trabalho.  

   ![Baixar config.jsem](./media/how-to-manage-workspace/configure.png)
   
   Coloque o ficheiro na estrutura do diretório com os seus scripts Python ou Cadernos Jupyter. Pode estar no mesmo diretório, num subdiretório chamado *.azureml,* ou num diretório-mãe. Quando cria uma instância de computação, este ficheiro é adicionado ao diretório correto no VM para si.
## <a name="find-a-workspace"></a><a name="view"></a>Encontre um espaço de trabalho

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. No campo de pesquisa superior, **escreva Machine Learning**.  

1. Selecione **Machine Learning**.

   ![Pesquisa de espaço de trabalho para aprendizagem automática Azure](./media/how-to-manage-workspace/find-workspaces.png)

1. Veja a lista de espaços de trabalho encontrados. Pode filtrar com base em subscrições, grupos de recursos e locais.  

1. Selecione um espaço de trabalho para exibir as suas propriedades.

## <a name="delete-a-workspace"></a>Eliminar uma área de trabalho

No [portal Azure,](https://portal.azure.com/) **selecione Delete**  na parte superior do espaço de trabalho que pretende eliminar.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Seleção privada de pontos finais":::

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="resource-provider-errors"></a>Erros do fornecedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Mover o espaço de trabalho

> [!WARNING]
> Mover o seu espaço de trabalho Azure Machine Learning para uma subscrição diferente, ou mover a subscrição proprietária para um novo inquilino, não é suportado. Fazê-lo pode causar erros.

### <a name="deleting-the-azure-container-registry"></a>Eliminação do Registo do Contentor de Azure

O espaço de trabalho Azure Machine Learning utiliza o Registo do Contentor Azure (ACR) para algumas operações. Criará automaticamente uma instância ACR quando precisa primeiro de uma.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Passos seguintes

Siga o tutorial de corpo inteiro para aprender a usar um espaço de trabalho para construir, treinar e implementar modelos com Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Modelos de comboio](tutorial-train-models-with-aml.md)
