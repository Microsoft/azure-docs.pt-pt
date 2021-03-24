---
title: Config Server em Azure Spring Cloud
description: Saiba como configurar uma instância do Servidor configurar a Nuvem de Nuvem de primavera para o seu Azure Spring Cloud no portal Azure
ms.service: spring-cloud
ms.topic: how-to
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.custom: devx-track-java
ms.openlocfilehash: de113e3c005e11bd2bcd13ec6c1554664ba8fbaf
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878254"
---
# <a name="set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Configurar uma instância do Servidor Config da Nuvem de primavera para o seu serviço

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

Este artigo mostra-lhe como ligar uma instância do Servidor Configurar de Nuvem de primavera ao seu serviço Azure Spring Cloud.

O Spring Cloud Config fornece suporte ao servidor e ao cliente para uma configuração externa num sistema distribuído. Com a instância do Config Server, você tem um lugar central para gerir propriedades externas para aplicações em todos os ambientes. Para obter mais informações, consulte a [referência do Servidor Config da Nuvem de primavera](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
* Um serviço Azure Spring Cloud já a provisionado e em funcionamento. Para configurar e lançar um serviço Azure Spring Cloud, consulte [Quickstart: Lançar uma aplicação Java Spring utilizando o Azure CLI](spring-cloud-quickstart.md).

## <a name="restriction"></a>Restrição

Existem algumas restrições quando se utiliza o Config Server com uma parte traseira do Git. Algumas propriedades são automaticamente injetadas no ambiente de aplicação para aceder ao Config Server e à Service Discovery. Se também configurar essas propriedades a partir dos seus ficheiros Config Server, poderá experimentar conflitos e comportamentos inesperados. As propriedades incluem: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
spring.jmx.enabled
```

> [!CAUTION]
> Recomendamos vivamente que _não_ coloque as propriedades acima nos ficheiros de aplicações do Config Server.

## <a name="create-your-config-server-files"></a>Crie os seus ficheiros Config Server

A Azure Spring Cloud suporta Azure DevOps, GitHub, GitLab e Bitbucket para armazenar os seus ficheiros Config Server. Quando tiver o seu repositório pronto, crie os ficheiros de configuração com as seguintes instruções e guarde-os lá.

Além disso, algumas propriedades configuráveis estão disponíveis apenas para certos tipos. As subsecções que se seguem listam as propriedades de cada tipo de repositório.

### <a name="public-repository"></a>Repositório público

Quando se usa um repositório público, as suas propriedades configuráveis são mais limitadas.

Todas as propriedades configuráveis que são usadas para configurar o repositório público Git estão listadas na tabela seguinte:

> [!NOTE]
> Usar um hífen (-) para separar palavras é a única convenção de nomeação que é atualmente apoiada. Por exemplo, pode utilizar *a etiqueta padrão,* mas não *padrãoLabel*.

| Propriedade        | Necessário | Funcionalidade                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Sim    | O URI do repositório git que é usado como o Config Server back end começa com *http://*, *https://*, *git@*, ou *ssh://*. |
| `default-label` | No     | A etiqueta padrão do repositório git, deve ser o nome do *ramo,* o nome da *etiqueta,* ou *identificação* do repositório. |
| `search-paths`  | No     | Uma variedade de cordas que são usadas para pesquisar subdiretivas do repositório de Git. |

------

### <a name="private-repository-with-ssh-authentication"></a>Repositório privado com autenticação SSH

Todas as propriedades configuráveis utilizadas para configurar o repositório privado git com SSH estão listadas na tabela seguinte:

> [!NOTE]
> Usar um hífen (-) para separar palavras é a única convenção de nomeação que é atualmente apoiada. Por exemplo, pode utilizar *a etiqueta padrão,* mas não *padrãoLabel*.

| Propriedade                   | Necessário | Funcionalidade                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Sim    | O URI do repositório git usado como back end do Config Server, deve ser iniciado com *http://*, *https://*, *git@* ou *ssh://*. |
| `default-label`            | No     | A etiqueta padrão do repositório git, deve ser o nome do *ramo,* o nome da *etiqueta,* ou *identificação* do repositório. |
| `search-paths`             | No     | Uma série de cordas usadas para pesquisar subdiretivas do repositório de Git. |
| `private-key`              | No     | A chave privada SSH para aceder ao repositório Git, _necessária_ quando o URI começa com *git@* ou *ssh://*. |
| `host-key`                 | No     | A chave hospedeira do servidor repositório Git não deve incluir o prefixo do algoritmo como coberto por `host-key-algorithm` . |
| `host-key-algorithm`       | No     | O algoritmo-chave do anfitrião, deve ser *ssh-dss,* *ssh-rsa,* *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384*, ou *ecdsa-sha2-nistp521*. *Obrigatório apenas* se `host-key` existir. |
| `strict-host-key-checking` | No     | Indica se a instância do Servidor Config não começará quando alavancar o privado `host-key` . Deve ser *verdadeiro* (valor predefinido) ou *falso*. |

> [!NOTE]
> Config Server toma `master` (om Git em si) como etiqueta padrão se não for especificado. Mas o GitHub mudou o ramo padrão de `master` `main` recentemente. Para evitar a falha do Servidor Config da Nuvem de primavera de Azure, preste atenção à etiqueta padrão ao configurar o Config Server com o GitHub, especialmente para novos repositórios criados.

-----

### <a name="private-repository-with-basic-authentication"></a>Repositório privado com autenticação básica

Todas as propriedades configuráveis utilizadas para configurar o repositório privado git com autenticação básica estão listadas abaixo.

> [!NOTE]
> Usar um hífen (-) para separar palavras é a única convenção de nomeação que é atualmente apoiada. Por exemplo, utilize *a etiqueta padrão*, não *o padrãoLabel*.

| Propriedade        | Necessário | Funcionalidade                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Sim    | O URI do repositório git que é usado como a parte de trás do Config Server deve ser iniciado com *http://*, *https://*, *git@*, ou *ssh://*. |
| `default-label` | No     | A etiqueta padrão do repositório git, deve ser o nome do *ramo,* o nome da *etiqueta,* ou *identificação* do repositório. |
| `search-paths`  | No     | Uma série de cordas usadas para pesquisar subdiretivas do repositório de Git. |
| `username`      | No     | O nome de utilizador que é usado para aceder ao servidor repositório git, _exigido_ quando o servidor do repositório Git suporta `Http Basic Authentication` . |
| `password`      | No     | A palavra-passe utilizada para aceder ao servidor repositório git, _necessária_ quando o servidor do repositório Git suporta `Http Basic Authentication` . |

> [!NOTE]
> Muitos `Git` servidores repositórios suportam o uso de fichas em vez de palavras-passe para a autenticação básica HTTP. Alguns repositórios, como o GitHub, permitem que os tokens persistam indefinidamente. No entanto, alguns servidores de repositórios git, incluindo Azure DevOps, forçam tokens a expirar em poucas horas. Os repositórios que fazem com que os tokens expirem não devem utilizar a autenticação baseada em símbolos com a Azure Spring Cloud.

### <a name="git-repositories-with-pattern"></a>Repositórios de git com padrão

Todas as propriedades configuráveis usadas para configurar repositórios de Git com padrão estão listadas abaixo.

> [!NOTE]
> Usar um hífen (-) para separar palavras é a única convenção de nomeação que é atualmente apoiada. Por exemplo, utilize *a etiqueta padrão*, não *o padrãoLabel*.

| Propriedade                           | Necessário         | Funcionalidade                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | No             | Um mapa que consiste nas definições de um repositório de Git com um nome próprio. |
| `repos."uri"`                      | Sim, em `repos` | O URI do repositório git que é usado como a parte de trás do Config Server deve ser iniciado com *http://*, *https://*, *git@*, ou *ssh://*. |
| `repos."name"`                     | Sim, em `repos` | Um nome para identificar no repositório de Git, _só requerido_ se `repos` existir. Por exemplo, *equipa A,* *equipa B.* |
| `repos."pattern"`                  | No             | Uma série de cordas usadas para combinar com um nome de aplicação. Para cada padrão, utilize o `{application}/{profile}` formato com wildcards. |
| `repos."default-label"`            | No             | A etiqueta padrão do repositório git, deve ser o nome do *ramo,* o nome da *etiqueta,* ou *identificação* do repositório. |
| `repos."search-paths`"             | No             | Uma série de cordas usadas para pesquisar subdiretivas do repositório de Git. |
| `repos."username"`                 | No             | O nome de utilizador que é usado para aceder ao servidor repositório git, _exigido_ quando o servidor do repositório Git suporta `Http Basic Authentication` . |
| `repos."password"`                 | No             | A palavra-passe utilizada para aceder ao servidor repositório git, _necessária_ quando o servidor do repositório Git suporta `Http Basic Authentication` . |
| `repos."private-key"`              | No             | A chave privada SSH para aceder ao repositório git, _necessária_ quando o URI começa com *git@* ou *ssh://*. |
| `repos."host-key"`                 | No             | A chave hospedeira do servidor repositório Git não deve incluir o prefixo do algoritmo como coberto por `host-key-algorithm` . |
| `repos."host-key-algorithm"`       | No             | O algoritmo-chave do anfitrião, deve ser *ssh-dss,* *ssh-rsa,* *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384*, ou *ecdsa-sha2-nistp521*. *Obrigatório apenas* se `host-key` existir. |
| `repos."strict-host-key-checking"` | No             | Indica se a instância do Servidor Config não começará quando alavancar o privado `host-key` . Deve ser *verdadeiro* (valor predefinido) ou *falso*. |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Anexe o seu repositório Config Server à Nuvem de primavera de Azure

Agora que os seus ficheiros de configuração são guardados num repositório, tem de ligar a Azure Spring Cloud a ele.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Vá à sua página de **visão geral da** nuvem de primavera Azure.

3. Selecione **Config Server** no painel de navegação esquerdo.

4. Na secção **de repositório padrão,** desafine o **URI** para https://github.com/Azure-Samples/piggymetrics-config " .

5. Clique **em Validar**.

    ![Navegue para o servidor config](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

6. Quando a validação estiver concluída, clique em **Aplicar** para guardar as suas alterações.

    ![Validação do servidor config](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

7. A atualização da configuração pode demorar alguns minutos.
 
    ![Atualizar o servidor config](media/spring-cloud-quickstart-launch-app-portal/updating-config.png) 

8. Deverá receber uma notificação quando a configuração estiver completa.

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Insira informações de repositório diretamente no portal Azure

#### <a name="default-repository"></a>Repositório padrão

* **Repositório público**: Na secção **de repositório padrão,** na caixa **Uri,** cole o URI do repositório.  Desconfige a **etiqueta.**  Certifique-se de que a **definição de autenticação** é **pública** e, em seguida, selecione **Aplicar** para terminar. 

* **Repositório privado**: Azure Spring Cloud suporta a autenticação básica baseada em password/token e SSH.

    * **Autenticação Básica**: Na secção **de repositório predefinido,** na caixa **Uri,** cole o URI do repositório e, em seguida, selecione o botão **autenticação** (ícone "lápis"). No painel **de autenticação de edição,** na lista de drop-down **do tipo autenticação,** selecione **HTTP Basic**, e, em seguida, insira o seu nome de utilizador e palavra-passe/token para garantir o acesso à Nuvem de primavera de Azure. Selecione **OK** e, em seguida, selecione **Aplicar** para terminar a configuração da sua instância Config Server.

    ![O painel de autenticação de edição auth básico](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Alguns servidores de repositório de Git, como o GitHub, utilizam um *token pessoal* ou um token de acesso , como uma *palavra-passe,* para **autenticação básica.** Podes usar esse tipo de símbolo como senha em Azure Spring Cloud, porque nunca expirará. Mas para outros servidores de repositório de Git, como Bitbucket e Azure DevOps, o *token de acesso* expira em uma ou duas horas. Isto significa que a opção não é viável quando se utiliza esses servidores de repositório com a Azure Spring Cloud.

    * **SSH**: Na secção **de repositório padrão,** na caixa **Uri,** cole o URI repositório e, em seguida, selecione o botão **autenticação** (ícone "lápis"). No painel **de autenticação de edição,** na lista de drop-down **do tipo autenticação,** selecione **SSH** e, em seguida, introduza a sua **tecla Privada**. Opcionalmente, especifique a **chave de anfitrião** e **o algoritmo da chave do anfitrião.** Certifique-se de incluir a sua chave pública no seu repositório Config Server. Selecione **OK** e, em seguida, selecione **Aplicar** para terminar a configuração da sua instância Config Server.

    ![O painel de autenticação de edição ssh auth](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Repositório de padrão

Se pretender utilizar um **repositório** de padrões opcional para configurar o seu serviço, especifique o **URI** e a **Autenticação** da mesma forma que o **repositório predefinido**. Certifique-se de incluir um **Nome** para o seu padrão e, em seguida, selecione **Aplicar** para anexá-lo ao seu exemplo. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Insira informações de repositório num ficheiro YAML

Se tiver escrito um ficheiro YAML com as suas definições de repositório, pode importar o ficheiro diretamente da sua máquina local para a Azure Spring Cloud. Um simples ficheiro YAML para um repositório privado com autenticação básica seria assim:

```yaml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Selecione o botão **Desembaraçar as definições** de Importação e, em seguida, selecione o ficheiro YAML do seu diretório de projetos. Selecione **Import**, e, em seguida, uma `async` operação das suas **Notificações** aparecerá. Após 1-2 minutos, deve reportar sucesso.

![O painel de notificações do servidor Config](media/spring-cloud-tutorial-config-server/local-yml-success.png)


As informações do seu ficheiro YAML devem ser apresentadas no portal Azure. **Selecione Aplicar** para terminar. 

## <a name="using-azure-repos-for-azure-spring-cloud-configuration"></a>Usando repos Azure para configuração de nuvem de primavera Azure

O Azure Spring Cloud pode aceder a repositórios Git que sejam públicos, estejam protegidos por SSH ou protegidos através da autenticação básica HTTP. Usaremos esta última opção, pois é mais fácil criar e gerir com o Azure Repos.

### <a name="get-repo-url-and-credentials"></a>Obtenha url de repo e credenciais
1. No portal Azure Repos para o seu projeto, clique no botão "Clone":

    ![Botão clone](media/spring-cloud-tutorial-config-server/clone-button.png)

1. Copie o URL do clone da caixa de texto. Este URL será tipicamente na forma:

    ```Text
    https://<organization name>@dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    Remova tudo depois `https://` e `dev.azure.com` antes, incluindo o `@` . O URL resultante deve estar na forma:

    ```Text
    https://dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    Guarde este URL para utilização na secção seguinte.

1. Clique em "Gerar Credenciais de Git". Aparecerá um nome de utilizador e uma palavra-passe. Guarde estes para utilização na secção seguinte.


### <a name="configure-azure-spring-cloud-to-access-the-git-repository"></a>Configurar o Azure Spring Cloud para aceder ao repositório Git

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Vá à sua página de **visão geral da** nuvem de primavera Azure.

1. Selecione o serviço para configurar.

1. No painel esquerdo da página de serviço, em **Definições,** selecione o **separador Servidor Config.** Configure o repositório que criamos anteriormente:
   - Adicione o URL repositório que guardou da secção anterior
   - Clique `Authentication` e selecione `HTTP Basic`
   - O __nome de utilizador__ é o nome de utilizador guardado na secção anterior
   - A __palavra-passe__ é a palavra-passe guardada na secção anterior
   - Clique em "Apply" e aguarde que a operação tenha sucesso

   ![Servidor de configuração do Spring Cloud](media/spring-cloud-tutorial-config-server/config-server-azure-repos.png)

## <a name="delete-your-app-configuration"></a>Elimine a configuração da sua aplicação

Depois de ter guardado um ficheiro de configuração, o botão **de configuração** da aplicação Delete aparece no **separador Configuração.** A seleção deste botão apagará completamente as definições existentes. Deve selecioná-lo se pretender ligar a sua instância do Config Server a outra fonte, como mudar-se de GitHub para Azure DevOps.



## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ativar e configurar a sua instância do Servidor Config da Nuvem de primavera. Para saber mais sobre a gestão da sua aplicação, consulte [Escala uma aplicação em Azure Spring Cloud.](spring-cloud-howto-scale-manual.md)
