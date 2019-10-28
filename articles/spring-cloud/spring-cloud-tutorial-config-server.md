---
title: Configurar seu servidor de configuração no Azure Spring Cloud | Microsoft Docs
description: Neste tutorial, você aprenderá a configurar um servidor de configuração do Spring Cloud para sua nuvem do Azure Spring na portal do Azure
services: spring-cloud
ms.service: spring-cloud
ms.topic: tutorial
ms.reviewer: jeconnoc
ms.author: v-vasuke
author: v-vasuke
ms.date: 08/08/2019
ms.openlocfilehash: 31ef82976a1c6938ae0bf591b2f8c8b1a0040466
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72928946"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-for-your-service"></a>Tutorial: configurar um servidor de configuração do Spring Cloud para seu serviço

Este tutorial mostrará como conectar um servidor de configuração do Spring Cloud ao serviço de nuvem do Azure Spring.

A configuração do Spring Cloud fornece suporte do lado do cliente e do servidor para configuração externa em um sistema distribuído. Com o servidor de configuração, você tem um local central para gerenciar Propriedades externas para aplicativos em todos os ambientes. Para saber mais, visite a [referência do servidor de configuração do Spring Cloud](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
* Um serviço de nuvem do Azure Spring já provisionado e em execução.  Conclua este guia de [início rápido](spring-cloud-quickstart-launch-app-cli.md) para provisionar e iniciar um serviço de nuvem do Azure Spring.

## <a name="restriction"></a>Restrição

Há algumas restrições quando você usa o __servidor de configuração__ com um back-end git. Algumas propriedades serão injetadas automaticamente em seu ambiente de aplicativo para acessar o __servidor de configuração__ e a descoberta de __serviço__. Se você também configurar essas propriedades de seus arquivos de **servidor de configuração** , poderá enfrentar conflitos e comportamento inesperado. As propriedades incluem: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```
> [!CAUTION]
> É altamente recomendável que você __não__ Coloque as propriedades acima em seus arquivos de aplicativo do __servidor de configuração__ .

## <a name="create-your-config-server-files"></a>Criar os arquivos do servidor de configuração

O Azure Spring Cloud dá suporte ao Azure DevOps, GitHub, GitLab e bitbucket para armazenar seus arquivos de servidor de configuração. Quando o repositório estiver pronto, faça com que os arquivos de configuração tenham as instruções abaixo e armazene-os lá.

Além disso, algumas propriedades configuráveis só estão disponíveis para alguns tipos. As subseções a seguir listam as propriedades de cada tipo de repositório.

### <a name="public-repository"></a>Repositório público

Ao usar um repositório público, suas propriedades configuráveis serão mais limitadas.

Todas as propriedades configuráveis usadas para configurar o repositório de `Git` público estão listadas abaixo.

> [!NOTE]
> Usar um hífen ("-") para separar palavras é a única Convenção de nomenclatura com suporte no momento. Por exemplo, use `default-label` não `defaultLabel`.

| Propriedade        | Obrigatório | Funcionalidade                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | O `uri` do repositório de `Git` usado como back-end do servidor de configuração, deve ser iniciado com `http://`, `https://`, `git@`ou `ssh://`. |
| `default-label` | `no`     | O rótulo padrão do repositório de `Git` deve ser o `branch name`, `tag name`ou `commit-id` do repositório. |
| `search-paths`  | `no`     | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório de `Git`. |

------

### <a name="private-repository-with-ssh-authentication"></a>Repositório privado com autenticação SSH

Todas as propriedades configuráveis usadas para configurar o repositório `Git` particular com `Ssh` estão listadas abaixo.

> [!NOTE]
> Usar um hífen ("-") para separar palavras é a única Convenção de nomenclatura com suporte no momento. Por exemplo, use `default-label` não `defaultLabel`.

| Propriedade                   | Obrigatório | Funcionalidade                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | `yes`    | O `uri` do repositório de `Git` usado como back-end do servidor de configuração, deve ser iniciado com `http://`, `https://`, `git@`ou `ssh://`. |
| `default-label`            | `no`     | O rótulo padrão do repositório de `Git` deve ser o `branch name`, `tag name`ou `commit-id` do repositório. |
| `search-paths`             | `no`     | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório de `Git`. |
| `private-key`              | `no`     | O `Ssh` chave privada para acessar o repositório de `Git`, __necessário__ quando `uri` começa com `git@` ou `ssh://`. |
| `host-key`                 | `no`     | A chave de host do servidor de repositório git não deve incluir o prefixo do algoritmo, conforme coberto pelo `host-key-algorithm`. |
| `host-key-algorithm`       | `no`     | O algoritmo de chave de host deve ser `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`ou `ecdsa-sha2-nistp521`. Necessário somente se `host-key` existir. |
| `strict-host-key-checking` | `no`     | Indica se o servidor de configuração não será iniciado ao aproveitar o `host-key`privado. Deve ser `true` (valor padrão) ou `false`. |

-----

### <a name="private-repository-with-basic-authentication"></a>Repositório privado com autenticação básica

Todas as propriedades configuráveis usadas para configurar o repositório git privado com a autenticação básica estão listadas abaixo.

> [!NOTE]
> Usar um hífen ("-") para separar palavras é a única Convenção de nomenclatura com suporte no momento. Por exemplo, use `default-label` não `defaultLabel`.

| Propriedade        | Obrigatório | Funcionalidade                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | `yes`    | O `uri` do repositório de `Git` usado como back-end do servidor de configuração, deve ser iniciado com `http://`, `https://`, `git@`ou `ssh://`. |
| `default-label` | `no`     | O rótulo padrão do repositório de `Git` deve ser o `branch name`, `tag name`ou `commit-id` do repositório. |
| `search-paths`  | `no`     | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório de `Git`. |
| `username`      | `no`     | O `username` usado para acessar o servidor de repositório de `Git`, __necessário__ quando o servidor de repositório `Git` dá suporte a `Http Basic Authentication`. |
| `password`      | `no`     | A senha usada para acessar o servidor de repositório de `Git`, __necessária__ quando o servidor de repositório de `Git` dá suporte a `Http Basic Authentication`. |

> [!NOTE]
> Alguns servidores de repositório `Git`, como o GitHub, dão suporte a um "token pessoal" ou "Access-token" como uma senha para `HTTP Basic Authentication`. Você também pode usar esse tipo de token como uma senha, e o "token pessoal" ou o "token de acesso" não expirará. No entanto, para servidores de repositório Git como BitBucket e DevOps do Azure, o token expirará em uma ou duas horas, tornando essa opção não viável para uso com o Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Repositórios git com padrão

Todas as propriedades configuráveis usadas para configurar repositórios git com o padrão são listadas abaixo.

> [!NOTE]
> Usar um hífen ("-") para separar palavras é a única Convenção de nomenclatura com suporte no momento. Por exemplo, use `default-label` não `defaultLabel`.

| Propriedade                           | Obrigatório         | Funcionalidade                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | `no`             | Um mapa que consiste nas configurações de um repositório de `Git` com um determinado nome. |
| `repos."uri"`                      | `yes` em `repos` | O `uri` do repositório de `Git` usado como back-end do servidor de configuração, deve ser iniciado com `http://`, `https://`, `git@`ou `ssh://`. |
| `repos."name"`                     | `yes` em `repos` | Um nome para identificar um repositório de `Git`, __necessário__ somente se `repos` existir. Por exemplo, acima, `team-A``team-B`. |
| `repos."pattern"`                  | `no`             | Uma matriz de cadeias de caracteres usada para corresponder a um nome de aplicativo. Para cada padrão, use o formato de `{application}/{profile}` com curingas. |
| `repos."default-label"`            | `no`             | O rótulo padrão do repositório de `Git` deve ser o `branch name`, `tag name`ou `commit-id` do repositório. |
| `repos."search-paths`"             | `no`             | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório de `Git`. |
| `repos."username"`                 | `no`             | O `username` usado para acessar o servidor de repositório de `Git`, __necessário__ quando o servidor de repositório `Git` dá suporte a `Http Basic Authentication`. |
| `repos."password"`                 | `no`             | A senha usada para acessar o servidor de repositório de `Git`, __necessária__ quando o servidor de repositório de `Git` dá suporte a `Http Basic Authentication`. |
| `repos."private-key"`              | `no`             | A chave privada `Ssh` para acessar o repositório `Git`, __necessária__ quando `uri` começa com `git@` ou `ssh://`. |
| `repos."host-key"`                 | `no`             | A chave de host do servidor de repositório git não deve incluir o prefixo do algoritmo, conforme coberto pelo `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | `no`             | O algoritmo de chave de host deve ser `ssh-dss`, `ssh-rsa`, `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`ou `ecdsa-sha2-nistp521`. __Necessário__ somente se `host-key` existir. |
| `repos."strict-host-key-checking"` | `no`             | Indica se o servidor de configuração não será iniciado ao aproveitar o `host-key`privado. Deve ser `true` (valor padrão) ou `false`. |

### <a name="import-applicationyml-file-from-spring-cloud-config"></a>Importar arquivo de `application.yml` da configuração do Spring Cloud

Você pode importar algumas configurações padrão do servidor de configuração diretamente do site de [configuração do Spring Cloud](https://spring.io/projects/spring-cloud-config) . Você pode fazer isso diretamente na portal do Azure, portanto, você não precisa executar as etapas agora para preparar os arquivos do servidor de configuração ou o repositório.

## <a name="attaching-your-config-server-repository-to-azure-spring-cloud"></a>Anexando o repositório do servidor de configuração ao Azure Spring Cloud

Agora que os arquivos de configuração estão salvos em um repositório, você precisa conectar o Azure Spring Cloud a ele.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Navegue até sua página de **visão geral** do Azure Spring Cloud.

1. Vá para a guia **servidor de configuração** no título **configurações** no menu no lado esquerdo.

### <a name="public-repository"></a>Repositório público

Se o repositório for público, basta clicar no botão **público** e colar a URL.

### <a name="private-repository"></a>Repositório privado

O Azure Spring Cloud dá suporte à autenticação SSH. Siga as instruções no portal do Azure para adicionar a chave pública ao seu repositório. Em seguida, certifique-se de incluir sua chave privada no arquivo de configuração.

Clique em **aplicar** para concluir a configuração do servidor de configuração.

## <a name="delete-your-app-configuration"></a>Excluir a configuração do aplicativo

Depois de salvar um arquivo de configuração, o botão **Excluir configuração de aplicativo** aparecerá na guia **configuração** . Isso apagará as configurações existentes completamente. Você deve fazer isso se desejar conectar seu servidor de configuração a outra fonte, como mudar do GitHub para o Azure DevOps.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a habilitar e configurar o servidor de configuração. Para saber mais sobre como gerenciar seu aplicativo, continue no tutorial para dimensionar manualmente seu aplicativo.

> [!div class="nextstepaction"]
> [Saiba como dimensionar manualmente seu aplicativo do Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

