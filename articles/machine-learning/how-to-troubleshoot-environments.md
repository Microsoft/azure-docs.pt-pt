---
title: Imagens ambientais de resolução de problemas
titleSuffix: Azure Machine Learning
description: Saiba como resolver problemas com as construções de imagens ambientais e instalações de pacotes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: b452c24b4b2ed6021910f267b9941f3829acccd8
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733396"
---
# <a name="troubleshoot-environment-image-builds"></a>A imagem do ambiente de resolução de problemas constrói
Saiba como resolver problemas com a imagem do ambiente Docker e as instalações de pacotes.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **subscrição do Azure**. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* [O Azure Machine Learning SDK.](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)
* O [Azure CLI.](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* A [extensão CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Para depurar localmente, você deve ter uma instalação Docker funcionando no seu sistema local.

## <a name="docker-image-build-failures"></a>Falhas de construção de imagem de estivador
 
Para a maioria das falhas de construção de imagem, a causa raiz pode ser encontrada no log de construção de imagem.
Pode encontrar o registo de construção de imagens a partir do portal Azure Machine Learning (20 \_ \_log.txt de construção de \_ imagem) ou a partir das suas tarefas ACR executa registos
 
Na maioria dos casos, é mais fácil reproduzir erros localmente. Verifique o tipo de erro e experimente um dos `setuptools` seguintes:

- Instale a dependência da conda localmente `conda install suspicious-dependency==X.Y.Z`
- Instale a dependência do pip localmente `pip install suspicious-dependency==X.Y.Z`
- Tente materializar todo o ambiente `conda create -f conda-specification.yml`

> [!IMPORTANT]
> Certifique-se de que a plataforma e o intérprete no seu cálculo local correspondem aos do telecomando. 

### <a name="timeout"></a>Tempo Limite 
 
Problemas de tempo limite podem acontecer para vários problemas de rede:
- Baixa largura de banda da internet
- Problemas com o servidor
- Grande dependência que não pode ser descarregada com as definições de tempo limite de conda ou pip
 
Mensagens semelhantes às abaixo indicarão o problema:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Soluções possíveis:
 
- Experimente uma fonte diferente para a dependência, se disponível, como espelhos, armazenamento de bolhas ou outros alimentos para a pitão.
- Atualizar conda ou pip. Se for utilizado um ficheiro de estivador personalizado, atualize as definições de tempo limite.
- Algumas versões pip têm problemas conhecidos. Considere adicionar uma versão específica do pip às dependências ambientais.

### <a name="package-not-found"></a>Pacote não encontrado

Este é o caso mais comum para falhas na construção de imagens.

- Pacote Conda não foi encontrado
        ```
        ResolvePackageNotFound: 
          - not-existing-conda-package
        ```

- Pacote ou versão de pip especificado não foi encontrado
        ```
        ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
        ERROR: No matching distribution found for invalid-pip-package
        ```

- Má dependência do pip aninhado
        ```
        ERROR: No matching distribution found for bad-backage==0.0 (from good-package==1.0)
        ```

Verifique se a embalagem existe nas fontes especificadas. Utilize [a pesquisa de pip](https://pip.pypa.io/en/stable/reference/pip_search/) para verificar as dependências do pip.

`pip search azureml-core`

Para dependências de conda, utilize [a pesquisa conda](https://docs.conda.io/projects/conda/en/latest/commands/search.html).

`conda search conda-forge::numpy`

Para mais opções:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Notas do instalador

Certifique-se de que existe a distribuição necessária para a versão especificada da plataforma e do intérprete Python.

Para as dependências de pip, navegue `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` para ver se a versão necessária está disponível. Por exemplo, https://pypi.org/project/azureml-core/1.11.0/#files

Para obter dependências conda, consulte o pacote no repositório do canal.
Para canais mantidos pela Anaconda, Inc., consulte [aqui.](https://repo.anaconda.com/pkgs/)

### <a name="pip-package-update"></a>Atualização do pacote pip

Durante a instalação ou atualização de um pacote de pip, o resolver poderá ter de atualizar um pacote já instalado para satisfazer os novos requisitos.
Desinstalar pode falhar por várias razões relacionadas com a versão pip ou a forma como a dependência foi instalada.
O cenário mais comum é que uma dependência instalada pela conda não poderia ser desinstalada por pip.
Para este cenário, considere desinstalar a dependência utilizando `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Problemas de instalação

Algumas versões de instalador têm problemas no pacote que podem levar a uma falha de construção.

Se for utilizada uma imagem base personalizada ou um ficheiro de estivador, recomendamos a utilização da versão conda 4.5.4 ou superior.

O pacote Pip é necessário para instalar dependências de pip e se uma versão não for especificada no ambiente, a versão mais recente será utilizada.
Recomendamos a utilização de uma versão conhecida do pip para evitar problemas transitórios ou alterações que possam ser causadas pela versão mais recente da ferramenta.

Considere fixar a versão pip no seu ambiente se vir alguma das mensagens abaixo:

`Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.`

`Pip subprocess error:
ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.`

Além disso, a instalação do pip pode ficar presa num ciclo infinito se houver conflitos irresolúveis nas dependências. Se trabalhar localmente, reduza a versão pip para < 20.3. Num ambiente conda criado a partir de um ficheiro YAML, esta questão só será vista se a conda-forge for forrão forrou o canal de maior prioridade. Para atenuar o problema, especifique explicitamente o pip < 20.3 (!=20.3 ou =20.2.4 pino para outra versão) como uma dependência conda no ficheiro de especificação conda.

## <a name="service-side-failures"></a>Falhas laterais de serviço

### <a name="unable-to-pull-image-from-mcraddress-could-not-be-resolved-for-container-registry"></a>Não foi possível retirar a imagem do MCR/Address não foi possível ser resolvida para o Registo de Contentores.
Possíveis questões:
- O nome do caminho para o registo do contentor pode não estar a ser corrigido corretamente. Verifique se os nomes de imagem usam barras duplas e a direção dos cortes nos anfitriões Linux vs Windows está correta.
- Se o ACR por trás de um Vnet estiver a utilizar um ponto final privado [numa região não suportada,](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)configuure o ACR por trás de um VNet utilizando o ponto final de serviço (acesso público) do portal e refaça.
- Depois de colocar o ACR atrás de um VNet, certifique-se de que o [modelo ARM](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) é executado. Isto permite que o espaço de trabalho comunique com a instância ACR.

### <a name="401-error-from-workspace-acr"></a>Erro de 401 do espaço de trabalho ACR
Resincronizar as chaves de armazenamento utilizando [ws.sync_keys()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--)

### <a name="environment-keeps-throwing-waiting-for-other-conda-operations-to-finish-error"></a>Ambiente continua a lançar "À espera que outras operações da Conda acabem..." Erro
Quando uma construção de imagem está em andamento, conda é bloqueado pelo cliente SDK. Se o processo se despenhou ou foi cancelado incorretamente pelo utilizador - a Conda permanece no estado bloqueado. Para resolver isto, elimine manualmente o ficheiro de bloqueio. 

### <a name="custom-docker-image-not-in-registry"></a>Imagem personalizada do estivador não no registo
Verifique se a [etiqueta correta](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment) é utilizada e isso `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` desativa a Conda e utiliza as embalagens instaladas do utilizador.

### <a name="common-vnet-issues"></a>Questões vnet comuns

1. Verifique se a conta de armazenamento, o cluster compute e o registo do contentor Azure estão todos na mesma sub-rede da rede virtual.
2. Quando o ACR está por trás de um VNet, não pode ser usado diretamente para construir imagens. O cluster computacional precisa de ser usado para construir imagens.
3. O armazenamento pode ter de ser colocado atrás de um VNet quando:
    - Utilização de inferenculação ou roda privada
    - Ver 403 erros de serviço (não autorizados)
    - Não é possível obter detalhes de imagem a partir de ACR/MCR

### <a name="image-build-fails-when-trying-to-access-network-protected-storage"></a>A construção de imagens falha ao tentar aceder ao armazenamento protegido da rede
- As tarefas ACR não funcionam atrás do VNet. Se o utilizador tiver o seu ACR por trás do VNet, tem de utilizar o cluster de cálculo para construir uma imagem.
- O armazenamento deve estar por trás do VNet para poder retirar as dependências dele. 

### <a name="cannot-run-experiments-when-storage-has-network-security-enabled"></a>Não é possível executar experiências quando o armazenamento tem segurança de rede ativada
Ao utilizar imagens padrão do Docker e ativar dependências geridas pelo utilizador, deve utilizar as [tags](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) de serviço MicrosoftContainerRegistry e AzureFrontDoor.FirstParty para permitir a lista de MCR e as suas dependências.

 Consulte [o VNET ativando](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) para mais informações.

### <a name="creating-an-icm"></a>Criar um ICM

Ao criar/atribuir um ICM à Metastore, por favor inclua o bilhete de suporte CSS para que possamos entender melhor o problema.

## <a name="next-steps"></a>Passos seguintes

- [Treine um modelo de aprendizagem automática para categorizar flores](how-to-train-scikit-learn.md)
- [Treine um modelo de aprendizagem automática usando uma imagem personalizada do Docker](how-to-train-with-custom-image.md)