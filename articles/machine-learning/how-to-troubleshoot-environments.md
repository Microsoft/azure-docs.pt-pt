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
ms.openlocfilehash: 5b2f62e8e04bddadc7068eb75405bcf1568f5713
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657303"
---
# <a name="troubleshoot-environment-image-builds"></a>A imagem do ambiente de resolução de problemas constrói

Saiba como resolver problemas com a imagem do ambiente Docker e as instalações de pacotes.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* [O Azure Machine Learning SDK.](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)
* O [Azure CLI.](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* A [extensão CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Para depurar localmente, você deve ter uma instalação Docker funcionando no seu sistema local.

## <a name="docker-image-build-failures"></a>Falhas na construção de imagem de estiva
 
Para a maioria das falhas de construção de imagem, você encontrará a causa principal no registo de construção de imagem.
Encontre o registo de construção de imagens a partir do portal Azure Machine Learning (20 \_log.txt \_ de \_ imagem) ou a partir dos registos de funcionamento da tarefa do Registo de Contentores Azure.
 
Normalmente é mais fácil reproduzir erros localmente. Verifique o tipo de erro e experimente um dos `setuptools` seguintes:

- Instale uma dependência conda localmente: `conda install suspicious-dependency==X.Y.Z` .
- Instale uma dependência de pip localmente: `pip install suspicious-dependency==X.Y.Z` .
- Tente materializar todo o ambiente: `conda create -f conda-specification.yml` .

> [!IMPORTANT]
> Certifique-se de que a plataforma e o intérprete do seu cluster de computação local correspondem aos do cluster de computação remota. 

### <a name="timeout"></a>Tempo Limite 
 
Os seguintes problemas de rede podem causar erros de tempo limite:

- Baixa largura de banda da internet
- Problemas com o servidor
- Grandes dependências que não podem ser descarregadas com as definições de tempo limite de conda ou pip
 
Mensagens semelhantes aos seguintes exemplos indicarão o problema:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Se receber uma mensagem de erro, experimente uma das seguintes soluções possíveis:
 
- Experimente uma fonte diferente, como espelhos, Azure Blob Storage, ou outros feeds Python, para a dependência.
- Atualizar conda ou pip. Se estiver a utilizar um ficheiro Docker personalizado, atualize as definições de tempo limite.
- Algumas versões pip têm problemas conhecidos. Considere adicionar uma versão específica do pip às dependências ambientais.

### <a name="package-not-found"></a>Pacote não encontrado

Os seguintes erros são mais comuns para falhas na construção de imagens:

- O pacote Conda não foi encontrado:

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- O pacote ou versão de pip especificado não foi encontrado:

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- Má dependência do pip aninhado:

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

Verifique se a embalagem existe nas fontes especificadas. Utilize [a pesquisa de pip](https://pip.pypa.io/en/stable/reference/pip_search/) para verificar as dependências do pip:

- `pip search azureml-core`

Para dependências de conda, utilize [a pesquisa conda:](https://docs.conda.io/projects/conda/en/latest/commands/search.html)

- `conda search conda-forge::numpy`

Para mais opções, tente:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Notas do instalador

Certifique-se de que existe a distribuição necessária para a versão especificada da plataforma e do intérprete Python.

Para as dependências de pip, vá `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` ver se a versão necessária está disponível. Vá https://pypi.org/project/azureml-core/1.11.0/#files ver um exemplo.

Para obter dependências conda, verifique o pacote no repositório do canal.
Para os canais mantidos pela Anaconda, Inc., consulte a [página Pacotes Anaconda](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Atualização do pacote pip

Durante uma instalação ou uma atualização de um pacote de pip, o resolver poderá ter de atualizar um pacote já instalado para satisfazer os novos requisitos.
A desinstalação pode falhar por várias razões relacionadas com a versão pip ou a forma como a dependência foi instalada.
O cenário mais comum é que uma dependência instalada pela conda não poderia ser desinstalada por pip.
Para este cenário, considere desinstalar a dependência utilizando `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Problemas de instalação

Algumas versões de instalador têm problemas no pacote que podem levar a uma falha de construção.

Se estiver a utilizar uma imagem base personalizada ou o Dockerfile, recomendamos a utilização da versão conda 4.5.4 ou posterior.

É necessário um pacote de pip para instalar dependências de pip. Se uma versão não for especificada no ambiente, a versão mais recente será utilizada.
Recomendamos a utilização de uma versão conhecida do pip para evitar problemas transitórios ou alterações que a versão mais recente da ferramenta possa causar.

Considere fixar a versão pip no seu ambiente se vir a seguinte mensagem:

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

Erro do subprocessamento do Pip:
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

A instalação do Pip pode ficar presa num ciclo infinito se houver conflitos irresolúveis nas dependências. Se estiver a trabalhar localmente, reduza a versão pip para < 20,3. Num ambiente conda criado a partir de um ficheiro YAML, só verá este problema se a Conda-forge for for for for o canal de maior prioridade. Para atenuar o problema, especifique explicitamente o pip < 20.3 (!=20.3 ou =20.2.4 pino para outra versão) como uma dependência conda no ficheiro de especificação conda.

## <a name="service-side-failures"></a>Falhas do lado do serviço

Consulte os seguintes cenários para resolver possíveis falhas do lado do serviço.

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>Não é possível tirar uma imagem de um registo de contentores, ou o endereço não poderia ser resolvido para um registo de contentores.

Possíveis questões:
- O nome do caminho para o registo do contentor pode não estar a ser corretamente corrigido. Verifique se os nomes de imagem usam barras duplas e a direção dos cortes nos anfitriões Linux contra Windows está correta.
- Se um registo de contentores por detrás de uma rede virtual estiver a utilizar um ponto final privado numa [região não suportada,](../private-link/private-link-overview.md#availability)configure o registo do contentor utilizando o ponto final de serviço (acesso público) a partir do portal e refaça.
- Depois de colocar o registo do contentor atrás de uma rede virtual, execute o [modelo do Gestor de Recursos Azure](./how-to-network-security-overview.md) para que o espaço de trabalho possa comunicar com a instância de registo do contentor.

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>Obtém-se um erro de 401 de um registo de contentores de espaço de trabalho

Resincronizar as chaves de armazenamento utilizando [ws.sync_keys()](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--).

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>O ambiente continua a lançar um "À espera que outras operações da ConDA terminem..." erro

Quando uma construção de imagem está em andamento, conda é bloqueado pelo cliente SDK. Se o processo se despenhou ou foi cancelado incorretamente pelo utilizador, a Conda permanece no estado bloqueado. Para resolver este problema, elimine manualmente o ficheiro de bloqueio. 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>A tua imagem personalizada do Docker não está no registo.

Verifique se a [etiqueta correta](./how-to-use-environments.md#create-an-environment) é utilizada e isso `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` desativa a conda e utiliza as embalagens instaladas do utilizador.

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>Obtém-se um dos seguintes problemas de rede virtual comuns

- Verifique se a conta de armazenamento, o cluster computacional e o registo de contentores estão todos na mesma sub-rede da rede virtual.
- Quando o registo do contentor está por detrás de uma rede virtual, não pode ser usado diretamente para construir imagens. Terá de usar o cluster computacional para construir imagens.
- O armazenamento poderá ter de ser colocado atrás de uma rede virtual se:
    - Utilize inferencing ou roda privada.
    - Consulte 403 erros de serviço (não autorizados).
    - Não consigo obter detalhes de imagem do Registo de Contentores Azure.

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>A construção de imagens falha quando se está a tentar aceder ao armazenamento protegido da rede

- As tarefas do Registo de Contentores Azure não funcionam por trás de uma rede virtual. Se o utilizador tiver o seu registo de contentores atrás de uma rede virtual, tem de utilizar o cluster de cálculo para construir uma imagem.
- O armazenamento deve estar por trás de uma rede virtual para retirar as dependências dela.

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>Não é possível fazer experiências quando o armazenamento tem segurança de rede ativada

Se estiver a utilizar imagens padrão do Docker e a permitir dependências geridas pelo utilizador, utilize as [tags](./how-to-network-security-overview.md) de serviço MicrosoftContainerRegistry e AzureFrontDoor.FirstParty para permitir o Registo de Contentores Azure E as suas dependências.

 Para obter mais informações, consulte [Ativar redes virtuais.](./how-to-network-security-overview.md)

### <a name="you-need-to-create-an-icm"></a>Você precisa criar um ICM

Quando estiver a criar/atribuir um ICM à Metastore, inclua o bilhete de suporte CSS para que possamos entender melhor o problema.

## <a name="next-steps"></a>Passos seguintes

- [Treine um modelo de aprendizagem automática para categorizar flores](how-to-train-scikit-learn.md)
- [Treine um modelo de aprendizagem automática usando uma imagem personalizada do Docker](how-to-train-with-custom-image.md)
