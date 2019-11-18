---
title: Configurar a instância do servidor de configuração no Azure Spring Cloud | Microsoft Docs
description: Neste tutorial, você aprenderá a configurar uma instância do servidor de configuração do Spring Cloud para sua nuvem do Azure Spring na portal do Azure
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/18/2019
ms.openlocfilehash: 7589a3a750e2fe04736bb3c8fc072c7a2c0a7358
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147549"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Tutorial: configurar uma instância de servidor de configuração do Spring Cloud para seu serviço

Este artigo mostra como conectar uma instância do servidor de configuração do Spring Cloud ao serviço de nuvem do Azure Spring.

A configuração do Spring Cloud fornece suporte do lado do cliente e do servidor para uma configuração externa em um sistema distribuído. Com a instância do servidor de configuração, você tem um local central para gerenciar Propriedades externas para aplicativos em todos os ambientes. Para obter mais informações, consulte [referência do servidor de configuração do Spring Cloud](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
* Um serviço de nuvem do Azure Spring já provisionado e em execução. Para configurar e iniciar um serviço de nuvem do Azure Spring, consulte [início rápido: iniciar um aplicativo Spring Java usando o CLI do Azure](spring-cloud-quickstart-launch-app-cli.md).

## <a name="restriction"></a>Restrição

Há algumas restrições quando você usa o servidor de configuração com um back-end do git. Algumas propriedades são injetadas automaticamente em seu ambiente de aplicativo para acessar o servidor de configuração e a descoberta de serviço. Se você também configurar essas propriedades de seus arquivos de servidor de configuração, poderá enfrentar conflitos e comportamento inesperado. As propriedades incluem: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```

> [!CAUTION]
> É altamente recomendável que você _não_ Coloque as propriedades acima em seus arquivos de aplicativo do servidor de configuração.

## <a name="create-your-config-server-files"></a>Criar os arquivos do servidor de configuração

O Azure Spring Cloud dá suporte ao Azure DevOps, GitHub, GitLab e bitbucket para armazenar seus arquivos de servidor de configuração. Quando o repositório estiver pronto, crie os arquivos de configuração com as instruções a seguir e armazene-os lá.

Além disso, algumas propriedades configuráveis estão disponíveis apenas para determinados tipos. As subseções a seguir listam as propriedades de cada tipo de repositório.

### <a name="public-repository"></a>Repositório público

Quando você usa um repositório público, suas propriedades configuráveis são mais limitadas.

Todas as propriedades configuráveis que são usadas para configurar o repositório git público são listadas na tabela a seguir:

> [!NOTE]
> Usar um hífen (-) para separar palavras é a única Convenção de nomenclatura com suporte no momento. Por exemplo, você pode usar o *rótulo padrão*, mas não *defaultlabel*.

| Propriedade        | Necessário | Funcionalidade                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Sim    | O URI do repositório git que é usado como o back-end do servidor de configuração começa com *http://* , *https://* , *git@* ou *SSH://* . |
| `default-label` | Não     | O rótulo padrão do repositório git deve ser o nome da *ramificação*, o *nome da marca*ou a ID da *confirmação* do repositório. |
| `search-paths`  | Não     | Uma matriz de cadeias de caracteres que são usadas para pesquisar subdiretórios do repositório git. |

------

### <a name="private-repository-with-ssh-authentication"></a>Repositório privado com autenticação SSH

Todas as propriedades configuráveis usadas para configurar o repositório git privado com o SSH estão listadas na tabela a seguir:

> [!NOTE]
> Usar um hífen (-) para separar palavras é a única Convenção de nomenclatura com suporte no momento. Por exemplo, você pode usar o *rótulo padrão*, mas não *defaultlabel*.

| Propriedade                   | Necessário | Funcionalidade                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Sim    | O URI do repositório git usado como o back-end do servidor de configuração deve ser iniciado com *http://* , *https://* , *git@* ou *SSH://* . |
| `default-label`            | Não     | O rótulo padrão do repositório git deve ser o nome da *ramificação*, o *nome da marca*ou a ID da *confirmação* do repositório. |
| `search-paths`             | Não     | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório git. |
| `private-key`              | Não     | A chave privada SSH para acessar o repositório git, _necessária_ quando o URI começa com *git@* ou *SSH://* . |
| `host-key`                 | Não     | A chave de host do servidor de repositório git não deve incluir o prefixo do algoritmo, conforme coberto pelo `host-key-algorithm`. |
| `host-key-algorithm`       | Não     | O algoritmo de chave de host, deve ser *SSH-DSS*, *SSH-RSA*, *ECDSA-SHA2-nistp256*, *ECDSA-SHA2-nistp384*ou *ECDSA-SHA2-nistp521*. *Necessário* somente se `host-key` existir. |
| `strict-host-key-checking` | Não     | Indica se a instância do servidor de configuração não será iniciada ao aproveitar o `host-key`privado. Deve ser *true* (valor padrão) ou *false*. |

-----

### <a name="private-repository-with-basic-authentication"></a>Repositório privado com autenticação básica

Todas as propriedades configuráveis usadas para configurar o repositório git privado com a autenticação básica estão listadas abaixo.

> [!NOTE]
> Usar um hífen (-) para separar palavras é a única Convenção de nomenclatura com suporte no momento. Por exemplo, use o *rótulo padrão*, não *defaultlabel*.

| Propriedade        | Necessário | Funcionalidade                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Sim    | O URI do repositório git que é usado como o back-end do servidor de configuração deve ser iniciado com *http://* , *https://* , *git@* ou *SSH://* . |
| `default-label` | Não     | O rótulo padrão do repositório git deve ser o nome da *ramificação*, o *nome da marca*ou a ID da *confirmação* do repositório. |
| `search-paths`  | Não     | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório git. |
| `username`      | Não     | O nome de usuário que é usado para acessar o servidor de repositório git, _necessário_ quando o servidor de repositório git dá suporte a `Http Basic Authentication`. |
| `password`      | Não     | A senha usada para acessar o servidor de repositório git, _necessária_ quando o servidor de repositório git dá suporte a `Http Basic Authentication`. |

> [!NOTE]
> Muitos servidores de repositório `Git` dão suporte ao uso de tokens em vez de senhas para autenticação básica HTTP. Alguns repositórios, como o GitHub, permitem que os tokens persistam indefinidamente. No entanto, alguns servidores de repositório git, incluindo o Azure DevOps, forçam tokens a expirar em algumas horas. Os repositórios que fazem com que os tokens expirem não devem usar a autenticação baseada em token com o Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Repositórios git com padrão

Todas as propriedades configuráveis usadas para configurar repositórios git com o padrão são listadas abaixo.

> [!NOTE]
> Usar um hífen (-) para separar palavras é a única Convenção de nomenclatura com suporte no momento. Por exemplo, use o *rótulo padrão*, não *defaultlabel*.

| Propriedade                           | Necessário         | Funcionalidade                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Não             | Um mapa que consiste nas configurações de um repositório Git com um determinado nome. |
| `repos."uri"`                      | Sim no `repos` | O URI do repositório git que é usado como o back-end do servidor de configuração deve ser iniciado com *http://* , *https://* , *git@* ou *SSH://* . |
| `repos."name"`                     | Sim no `repos` | Um nome a ser identificado no repositório git, _necessário_ somente se `repos` existir. Por exemplo, *equipe-a*, *equipe B*. |
| `repos."pattern"`                  | Não             | Uma matriz de cadeias de caracteres usada para corresponder a um nome de aplicativo. Para cada padrão, use o formato de `{application}/{profile}` com curingas. |
| `repos."default-label"`            | Não             | O rótulo padrão do repositório git deve ser o nome da *ramificação*, o *nome da marca*ou a ID da *confirmação* do repositório. |
| `repos."search-paths`"             | Não             | Uma matriz de cadeias de caracteres usada para pesquisar subdiretórios do repositório git. |
| `repos."username"`                 | Não             | O nome de usuário que é usado para acessar o servidor de repositório git, _necessário_ quando o servidor de repositório git dá suporte a `Http Basic Authentication`. |
| `repos."password"`                 | Não             | A senha usada para acessar o servidor de repositório git, _necessária_ quando o servidor de repositório git dá suporte a `Http Basic Authentication`. |
| `repos."private-key"`              | Não             | A chave privada SSH para acessar o repositório git, _necessária_ quando o URI começa com *git@* ou *SSH://* . |
| `repos."host-key"`                 | Não             | A chave de host do servidor de repositório git não deve incluir o prefixo do algoritmo, conforme coberto pelo `host-key-algorithm`. |
| `repos."host-key-algorithm"`       | Não             | O algoritmo de chave de host, deve ser *SSH-DSS*, *SSH-RSA*, *ECDSA-SHA2-nistp256*, *ECDSA-SHA2-nistp384*ou *ECDSA-SHA2-nistp521*. *Necessário* somente se `host-key` existir. |
| `repos."strict-host-key-checking"` | Não             | Indica se a instância do servidor de configuração não será iniciada ao aproveitar o `host-key`privado. Deve ser *true* (valor padrão) ou *false*. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Anexar o repositório do servidor de configuração ao Azure Spring Cloud

Agora que os arquivos de configuração foram salvos em um repositório, você precisa conectar o Azure Spring Cloud a ele.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).

1. Acesse sua página de **visão geral** do Azure Spring Cloud.

1. Selecione o serviço a ser configurado.

1. No painel esquerdo da página serviço, em **configurações**, selecione a guia **servidor de configuração** .

![A janela do servidor de configuração](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Insira as informações do repositório diretamente no portal do Azure

#### <a name="default-repository"></a>Repositório padrão

* **Repositório público**: na seção **repositório padrão** , na caixa **URI** , Cole o URI do repositório.  Defina o **rótulo** como **configuração**. Verifique se a configuração de **autenticação** é **pública**e, em seguida, selecione **aplicar** ao concluir. 

* **Repositório privado**: o Azure Spring Cloud dá suporte à autenticação básica baseada em token/senha e SSH.

    * **Autenticação básica**: na seção **repositório padrão** , na caixa **URI** , Cole o URI do repositório e, em seguida, selecione o botão **autenticação** (ícone de "lápis"). No painel **Editar Autenticação** , na lista suspensa **tipo de autenticação** , selecione **http básico**e, em seguida, insira seu nome de usuário e senha/token para conceder acesso ao Azure Spring Cloud. Selecione **OK**e, em seguida, selecione **aplicar** para concluir a configuração da instância do servidor de configuração.

    ![O painel Editar Autenticação](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Alguns servidores de repositório git, como o GitHub, usam um *token pessoal* ou um *token de acesso*, como uma senha, para **autenticação básica**. Você pode usar esse tipo de token como uma senha no Azure Spring Cloud, pois ele nunca expirará. Mas para outros servidores de repositório git, como o bitbucket e o Azure DevOps, o *token de acesso* expira em uma ou duas horas. Isso significa que a opção não é viável quando você usa esses servidores de repositório com o Azure Spring Cloud.

    * **SSH**: na seção **repositório padrão** , na caixa **URI** , Cole o URI do repositório e, em seguida, selecione o botão **autenticação** (ícone de "lápis"). No painel **Editar Autenticação** , na lista suspensa **tipo de autenticação** , selecione **SSH**e, em seguida, insira sua **chave privada**. Opcionalmente, especifique a **chave do host** e o **algoritmo de chave do host**. Certifique-se de incluir sua chave pública no repositório do servidor de configuração. Selecione **OK**e, em seguida, selecione **aplicar** para concluir a configuração da instância do servidor de configuração.

    ![O painel Editar Autenticação](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Repositório de padrões

Se você quiser usar um **repositório padrão** opcional para configurar seu serviço, especifique o **URI** e a **autenticação** da mesma maneira que o **repositório padrão**. Certifique-se de incluir um **nome** para o padrão e, em seguida, selecione **aplicar** para anexá-lo à sua instância. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Inserir informações do repositório em um arquivo YAML

Se você tiver gravado um arquivo YAML com as configurações do repositório, poderá importar o arquivo diretamente do computador local para o Azure Spring Cloud. Um arquivo YAML simples para um repositório privado com autenticação básica ficaria assim:

```yml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Selecione o botão **configurações de importação** e, em seguida, selecione o arquivo YAML do diretório do projeto. Selecione **importar**e uma `async` operação de suas **notificações** será exibida. Após 1-2 minutos, ele deve relatar êxito.

![O painel de notificações do servidor de configuração](media/spring-cloud-tutorial-config-server/local-yml-success.png)


As informações do arquivo YAML devem ser exibidas no portal do Azure. Selecione **aplicar** para concluir. 


## <a name="delete-your-app-configuration"></a>Excluir a configuração do aplicativo

Depois de salvar um arquivo de configuração, o botão **Excluir configuração de aplicativo** aparecerá na guia **configuração** . a seleção desse botão apagará completamente as configurações existentes. Você deve selecioná-lo se quiser conectar sua instância do servidor de configuração a outra fonte, como mover do GitHub para o Azure DevOps.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a habilitar e configurar sua instância do servidor de configuração do Spring Cloud. Para saber mais sobre como gerenciar seu aplicativo, continue no tutorial sobre como dimensionar manualmente seu aplicativo.

> [!div class="nextstepaction"]
> [Tutorial: dimensionar um aplicativo no Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md)
