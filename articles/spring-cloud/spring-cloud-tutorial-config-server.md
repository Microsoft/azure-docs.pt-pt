---
title: Tutorial - Instale a sua instância config Server em Azure Spring Cloud
description: Neste tutorial, você aprende a configurar uma instância de Spring Cloud Config Server para a sua Nuvem de primavera Azure no portal Azure
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.openlocfilehash: 5e0b5633a153583117cfe0d90ec5c0e7c5f2a147
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76277535"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Tutorial: Instale uma instância do Servidor Config da Nuvem de primavera para o seu serviço

Este artigo mostra-lhe como ligar uma instância do Spring Cloud Config Server ao seu serviço Azure Spring Cloud.

A Spring Cloud Config fornece suporte ao servidor e ao cliente para uma configuração externa num sistema distribuído. Com a instância Config Server, você tem um lugar central para gerir propriedades externas para aplicações em todos os ambientes. Para mais informações, consulte a referência do [Servidor Config da Nuvem de primavera](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
* Um serviço azure spring cloud já aprovisionado e em execução. Para configurar e lançar um serviço Azure Spring Cloud, consulte [Quickstart: Launch a Java Spring aplica-se utilizando o Azure CLI](spring-cloud-quickstart-launch-app-cli.md).

## <a name="restriction"></a>Restrição

Existem algumas restrições quando utiliza o Config Server com uma extremidade traseira git. Algumas propriedades são automaticamente injetadas no seu ambiente de aplicação para aceder ao Config Server e ao Service Discovery. Se também configurar essas propriedades a partir dos seus ficheiros Config Server, poderá experimentar conflitos e comportamentos inesperados. As propriedades incluem: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```

> [!CAUTION]
> Recomendamos vivamente que _não_ coloque as propriedades acima nos ficheiros de aplicação do Config Server.

## <a name="create-your-config-server-files"></a>Crie os seus ficheiros Config Server

A Azure Spring Cloud suporta o Azure DevOps, GitHub, GitLab e Bitbucket para armazenar os seus ficheiros Config Server. Quando tiver o seu repositório pronto, crie os ficheiros de configuração com as seguintes instruções e guarde-os lá.

Além disso, algumas propriedades configuráveis estão disponíveis apenas para certos tipos. As seguintes subsecções listam as propriedades para cada tipo de repositório.

### <a name="public-repository"></a>Repositório público

Quando utiliza um repositório público, as suas propriedades configuráveis são mais limitadas.

Todas as propriedades configuráveis que são usadas para configurar o repositório git público estão listadas na tabela seguinte:

> [!NOTE]
> Usar um hífen (-) para separar palavras é a única convenção de nomeação que é atualmente apoiada. Por exemplo, pode utilizar *a etiqueta predefinida*, mas não *o rótulo predefinido*.

| Propriedade        | Necessário | Funcionalidade                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Sim    | O URI do repositório Git que é usado como a extremidade traseira do Config Server começa com *http://*, *https://,* *git@* ou *ssh://*. |
| `default-label` | Não     | A etiqueta predefinida do repositório Git deve ser o nome do *ramo,* *o nome*da etiqueta ou a *identificação* do repositório. |
| `search-paths`  | Não     | Uma variedade de cordas que são usadas para pesquisar subdiretórios do repositório Git. |

------

### <a name="private-repository-with-ssh-authentication"></a>Repositório privado com autenticação SSH

Todas as propriedades configuráveis utilizadas para configurar o repositório privado Git com SSH estão listadas na tabela seguinte:

> [!NOTE]
> Usar um hífen (-) para separar palavras é a única convenção de nomeação que é atualmente apoiada. Por exemplo, pode utilizar *a etiqueta predefinida*, mas não *o rótulo predefinido*.

| Propriedade                   | Necessário | Funcionalidade                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Sim    | O URI do repositório Git utilizado como back back back back, deve ser iniciado com *http://,* *https://,* *git@* ou *ssh://.* |
| `default-label`            | Não     | A etiqueta predefinida do repositório Git deve ser o nome do *ramo,* *o nome*da etiqueta ou a *identificação* do repositório. |
| `search-paths`             | Não     | Uma série de cordas usadas para pesquisar subdirectitórios do repositório Git. |
| `private-key`              | Não     | A chave privada SSH para aceder ao repositório Git, _necessária_ quando o URI começa com *git@* ou *ssh://*. |
| `host-key`                 | Não     | A chave hospedeira do servidor de repositório Git, não `host-key-algorithm`deve incluir o prefixo do algoritmo tal como coberto por . |
| `host-key-algorithm`       | Não     | O algoritmo-chave anfitrião, deve ser *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384*, ou *ecdsa-sha2-nistp521*. *Só é* `host-key` necessário se existir. |
| `strict-host-key-checking` | Não     | Indica se a instância do Config Server não `host-key`começará ao alavancar o privado . Deve ser *verdadeiro* (valor predefinido) ou *falso*. |

-----

### <a name="private-repository-with-basic-authentication"></a>Repositório privado com autenticação básica

Todas as propriedades configuráveis utilizadas para configurar o repositório privado Git com autenticação básica estão listadas abaixo.

> [!NOTE]
> Usar um hífen (-) para separar palavras é a única convenção de nomeação que é atualmente apoiada. Por exemplo, utilize a *etiqueta predefinida*, não *predefinidoLabel*.

| Propriedade        | Necessário | Funcionalidade                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Sim    | O URI do repositório Git que é usado como a extremidade traseira do Config Server deve ser iniciado com *http://*, *https://,* *git@* ou *ssh://*. |
| `default-label` | Não     | A etiqueta predefinida do repositório Git deve ser o nome do *ramo,* *o nome*da etiqueta ou a *identificação* do repositório. |
| `search-paths`  | Não     | Uma série de cordas usadas para pesquisar subdirectitórios do repositório Git. |
| `username`      | Não     | O nome de utilizador que é usado para aceder ao servidor de repositório Git, _necessário_ quando o servidor de repositório Git suporta `Http Basic Authentication`. |
| `password`      | Não     | A palavra-passe utilizada para aceder ao _required_ servidor de repositório Git, `Http Basic Authentication`necessária quando o servidor de repositório Git suporta . |

> [!NOTE]
> Muitos `Git` servidores repositórios suportam o uso de fichas em vez de senhas para a Autenticação Básica HTTP. Alguns repositórios, como o GitHub, permitem que os tokens persistam indefinidamente. No entanto, alguns servidores repositórios git, incluindo O Azure DevOps, forçam tokens a expirar em poucas horas. Os repositórios que causam a expiração dos tokens não devem utilizar a autenticação baseada em token com a Nuvem de primavera Azure.

### <a name="git-repositories-with-pattern"></a>Repositórios git com padrão

Todas as propriedades configuráveis usadas para configurar repositórios Git com padrão estão listadas abaixo.

> [!NOTE]
> Usar um hífen (-) para separar palavras é a única convenção de nomeação que é atualmente apoiada. Por exemplo, utilize a *etiqueta predefinida*, não *predefinidoLabel*.

| Propriedade                           | Necessário         | Funcionalidade                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Não             | Um mapa composto pelas definições para um repositório Git com um nome próprio. |
| `repos."uri"`                      | Sim, em`repos` | O URI do repositório Git que é usado como a extremidade traseira do Config Server deve ser iniciado com *http://*, *https://,* *git@* ou *ssh://*. |
| `repos."name"`                     | Sim, em`repos` | Um nome para identificar no repositório Git, _necessário_ apenas se `repos` existir. Por exemplo, *equipa A,* *equipa B.* |
| `repos."pattern"`                  | Não             | Uma série de cordas usadas para combinar com um nome de aplicação. Para cada padrão, `{application}/{profile}` utilize o formato com wildcards. |
| `repos."default-label"`            | Não             | A etiqueta predefinida do repositório Git deve ser o nome do *ramo,* *o nome*da etiqueta ou a *identificação* do repositório. |
| `repos."search-paths`"             | Não             | Uma série de cordas usadas para pesquisar subdirectitórios do repositório Git. |
| `repos."username"`                 | Não             | O nome de utilizador que é usado para aceder ao servidor de repositório Git, _necessário_ quando o servidor de repositório Git suporta `Http Basic Authentication`. |
| `repos."password"`                 | Não             | A palavra-passe utilizada para aceder ao _required_ servidor de repositório Git, `Http Basic Authentication`necessária quando o servidor de repositório Git suporta . |
| `repos."private-key"`              | Não             | A chave privada SSH para aceder ao repositório Git, _necessária_ quando o URI começa com *git@* ou *ssh://*. |
| `repos."host-key"`                 | Não             | A chave hospedeira do servidor de repositório Git, não `host-key-algorithm`deve incluir o prefixo do algoritmo tal como coberto por . |
| `repos."host-key-algorithm"`       | Não             | O algoritmo-chave anfitrião, deve ser *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384*, ou *ecdsa-sha2-nistp521*. *Só é* `host-key` necessário se existir. |
| `repos."strict-host-key-checking"` | Não             | Indica se a instância do Config Server não `host-key`começará ao alavancar o privado . Deve ser *verdadeiro* (valor predefinido) ou *falso*. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Fixe o seu repositório Config Server à Nuvem de primavera Azure

Agora que os seus ficheiros de configuração são guardados num repositório, precisa de ligar a Nuvem de primavera Azure a ele.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Vá à sua página de **visão geral** da Nuvem de primavera Azure.

1. Selecione o serviço para configurar.

1. No painel esquerdo da página de serviço, em **Definições,** selecione o separador **Config Server.**

![A janela do Servidor Config](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Insira informações de repositório diretamente no portal Azure

#### <a name="default-repository"></a>Repositório por defeito

* **Repositório público**: Na secção de **repositório predefinido,** na caixa **Uri,** cola o repositório URI.  Desloque a **etiqueta** para **config**. Certifique-se de que a definição de **Autenticação** é **pública**e, em seguida, selecione **Aplicar** para terminar. 

* **Repositório privado**: Azure Spring Cloud suporta autenticação básica baseada em palavra-passe/token e SSH.

    * **Autenticação Básica**: Na secção **de repositório Predefinido,** na caixa **Uri,** cola o rescrito URI e, em seguida, selecione o botão **Autenticação** (ícone "lápis"). No painel de **autenticação de edição,** na lista de drop-down do **tipo Autenticação,** selecione **HTTP Basic,** e introduza o seu nome de utilizador e palavra-passe/token para dar acesso à Nuvem de Mola Azure. Selecione **OK**, e, em seguida, selecione **Aplicar** para terminar a configuração da sua instância do Servidor Config.

    ![O painel de autenticação editar](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Alguns servidores de repositório git, como o GitHub, utilizam um *símbolo pessoal* ou um *token de acesso,* como uma palavra-passe, para **autenticação básica.** Podes usar esse tipo de ficha como senha na Nuvem de primavera do Azure, porque nunca expirará. Mas para outros servidores de repositóriog Git, como Bitbucket e Azure DevOps, o *sinal de acesso* expira em uma ou duas horas. Isto significa que a opção não é viável quando se utiliza os servidores de repositórios com a Nuvem de primavera Azure.

    * **SSH**: Na secção **de repositório Predefinido,** na caixa **Uri,** cola o rescrito URI e, em seguida, selecione o botão **Autenticação** (ícone "lápis"). No painel de **autenticação de edição,** na lista de abandono do **tipo autenticação,** selecione **SSH**e introduza a **sua chave Privada**. Opcionalmente, especifique a **sua chave anfitriã** e o algoritmo de chave **anfitrião**. Certifique-se de incluir a sua chave pública no seu repositório Config Server. Selecione **OK**, e, em seguida, selecione **Aplicar** para terminar a configuração da sua instância do Servidor Config.

    ![O painel de autenticação editar](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Repositório de padrões

Se pretender utilizar um **repositório** de padrão opcional para configurar o seu serviço, especifique o **URI** e **a Autenticação** da mesma forma que o **repositório Predefinido**. Certifique-se de incluir um **Nome** para o seu padrão e, em seguida, selecione **Aplicar** para anexá-lo à sua instância. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Introduza informações de repositório num ficheiro YAML

Se tiver escrito um ficheiro YAML com as suas definições de repositório, pode importar o ficheiro diretamente da sua máquina local para a Nuvem de primavera Azure. Um simples ficheiro YAML para um repositório privado com autenticação básica seria assim:

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

Selecione o botão de **definições de Importação** e, em seguida, selecione o ficheiro YAML do seu diretório de projeto. Selecione **Import** `async` , e, em seguida, uma operação a partir das suas **Notificações** aparecerá. Após 1-2 minutos, deve relatar o sucesso.

![O painel de notificações do Servidor Config](media/spring-cloud-tutorial-config-server/local-yml-success.png)


As informações do seu ficheiro YAML devem ser apresentadas no portal Azure. Selecione **Aplicar** para terminar. 


## <a name="delete-your-app-configuration"></a>Eliminar a configuração da sua aplicação

Depois de ter guardado um ficheiro de configuração, o botão de configuração da **aplicação Delete** aparece no separador **Configuração.** Deve selecioná-lo se pretender ligar a instância do Config Server a outra fonte, como passar de GitHub para Azure DevOps.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a ativar e configurar a sua instância de Spring Cloud Config Server. Para saber mais sobre a gestão da sua aplicação, continue a ser o tutorial sobre a escala manual da sua aplicação.

> [!div class="nextstepaction"]
> [Tutorial: Escala uma aplicação na Nuvem de primavera Azure](spring-cloud-tutorial-scale-manual.md)
