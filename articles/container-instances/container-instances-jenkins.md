---
title: Usar instâncias de contêiner do Azure como um agente de compilação Jenkins
description: Saiba como usar as instâncias de contêiner do Azure como um agente de compilação Jenkins.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 08/31/2018
ms.author: danlep
ms.openlocfilehash: ed000779940d9af7b1384873bf9fddd1cde79c71
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326023"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Usar instâncias de contêiner do Azure como um agente de compilação Jenkins

As ACI (instâncias de contêiner do Azure) fornecem um ambiente sob demanda, intermitente e isolado para executar cargas de trabalho em contêineres. Devido a esses atributos, o ACI faz uma ótima plataforma para executar trabalhos de Build do Jenkins em grande escala. Este artigo apresenta a implantação e o uso de um servidor Jenkins pré-configurado com o ACI como um destino de compilação.

Para obter mais informações sobre as instâncias de contêiner do Azure, consulte [sobre as instâncias de contêiner do Azure][about-aci].

## <a name="deploy-a-jenkins-server"></a>Implantar um servidor Jenkins

1. Na portal do Azure, selecione **criar um recurso** e procure **Jenkins**. Selecione a oferta Jenkins com um Publicador da **Microsoft**e, em seguida, selecione **criar**.

2. Insira as seguintes informações no formulário **básico** e, em seguida, selecione **OK**.

   - **Nome**: Insira um nome para a implantação do Jenkins.
   - **Nome de usuário**: Insira um nome para o usuário administrador da máquina virtual Jenkins.
   - **Tipo de autenticação**: Recomendamos uma chave pública SSH para autenticação. Se você selecionar essa opção, Cole uma chave pública SSH a ser usada para fazer logon na máquina virtual Jenkins.
   - **Assinatura**: Selecione uma subscrição do Azure.
   - **Grupo de recursos**: Crie um grupo de recursos ou selecione um existente.
   - **Local**: Selecione um local para o servidor Jenkins.

   ![Configurações básicas para a implantação do portal do Jenkins](./media/container-instances-jenkins/jenkins-portal-01.png)

3. No formulário **configurações adicionais** , conclua os seguintes itens:

   - **Tamanho**: Selecione a opção de dimensionamento apropriada para sua máquina virtual Jenkins.
   - **Tipo de disco da VM**: Especifique o **HDD** (unidade de disco rígido) ou **SSD** (unidade de estado sólido) para o servidor Jenkins.
   - **Rede virtual**: Selecione a seta se você quiser modificar as configurações padrão.
   - **Sub-redes**: Selecione a seta, verifique as informações e selecione **OK**.
   - **Endereço IP público**: Selecione a seta para dar ao endereço IP público um nome personalizado, configurar a SKU e definir o método de atribuição.
   - **Rótulo de nome de domínio**: Especifique um valor para criar uma URL totalmente qualificada para a máquina virtual Jenkins.
   - **Tipo de versão do Jenkins**: Selecione o tipo de liberação desejado nas opções: **LTS**, **compilação semanal**ou **Azure verificado**.

   ![Configurações adicionais para a implantação do portal do Jenkins](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Para a integração da entidade de serviço, selecione **automático (MSI)** para que as [identidades gerenciadas dos recursos do Azure][managed-identities-azure-resources] criem automaticamente uma identidade de autenticação para a instância Jenkins. Selecione **manual** para fornecer suas próprias credenciais de entidade de serviço.

5. Os agentes de nuvem configuram uma plataforma baseada em nuvem para trabalhos de Build do Jenkins. Para o propósito deste artigo, selecione **ACI**. Com o agente de nuvem ACI, cada trabalho de compilação Jenkins é executado em uma instância de contêiner.

   ![Configurações de integração de nuvem para a implantação do portal do Jenkins](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Quando tiver concluído as configurações de integração, selecione **OK**e, em seguida, selecione **OK** novamente no Resumo de validação. Selecione **criar** no resumo de **termos de uso** . O servidor Jenkins leva alguns minutos para ser implantado.

## <a name="configure-jenkins"></a>Configurar o Jenkins

1. Na portal do Azure, navegue até o grupo de recursos Jenkins, selecione a máquina virtual Jenkins e anote o nome DNS.

   ![Nome DNS em detalhes sobre a máquina virtual Jenkins](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Navegue até o nome DNS da VM Jenkins e copie a cadeia de caracteres SSH retornada.

   ![Instruções de logon do Jenkins com a cadeia de caracteres SSH](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Abra uma sessão de terminal em seu sistema de desenvolvimento e cole a cadeia de caracteres SSH da última etapa. Atualize `username` para o nome de usuário que você especificou quando implantou o servidor Jenkins.

4. Depois que a sessão estiver conectada, execute o seguinte comando para recuperar a senha de administrador inicial:

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Deixe a sessão SSH e o túnel em execução e vá `http://localhost:8080` para em um navegador. Cole a senha inicial do administrador na caixa e selecione **continuar**.

   ![Tela "desbloquear Jenkins" com a caixa da senha do administrador](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Selecione **instalar plug-ins sugeridos** para instalar todos os plug-ins recomendados do Jenkins.

   ![Tela "Personalizar Jenkins" com "instalar plug-ins sugeridos" selecionado](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Crie uma conta de usuário administrador. Essa conta é usada para fazer logon no e trabalhar com sua instância do Jenkins.

   ![Tela "criar primeiro usuário administrador", com credenciais preenchidas](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Selecione **salvar e concluir**e, em seguida, selecione **começar a usar Jenkins** para concluir a configuração.

O Jenkins agora está configurado e pronto para compilar e implantar código. Para este exemplo, um aplicativo Java simples é usado para demonstrar uma compilação Jenkins em instâncias de contêiner do Azure.

## <a name="create-a-build-job"></a>Criar um trabalho de compilação

Agora, um trabalho de compilação Jenkins é criado para demonstrar o Jenkins Builds em uma instância de contêiner do Azure.

1. Selecione **novo item**, dê um nome ao projeto de compilação, como **ACI-demo**, selecione **projeto Freestyle**e selecione **OK**.

   ![Box para o nome do trabalho de compilação e lista de tipos de projeto](./media/container-instances-jenkins/jenkins-new-job.png)

2. Em **geral**, verifique se a seção **restringir onde este projeto pode ser executado** está selecionada. Digite **Linux** para a expressão de rótulo. Essa configuração garante que esse trabalho de compilação seja executado na nuvem ACI.

   ![Guia "geral" com detalhes de configuração](./media/container-instances-jenkins/jenkins-job-01.png)

3. Em **Compilar**, selecione **Adicionar etapa de compilação** e selecione **executar Shell**. Insira `echo "aci-demo"` como o comando.

   ![Guia "Compilar" com seleções para a etapa de compilação](./media/container-instances-jenkins/jenkins-job-02.png)

5. Selecione **Guardar**.

## <a name="run-the-build-job"></a>Executar o trabalho de compilação

Para testar o trabalho de compilação e observar as instâncias de contêiner do Azure como a plataforma de compilação, inicie manualmente uma compilação.

1. Selecione **Compilar agora** para iniciar um trabalho de compilação. Leva alguns minutos para que o trabalho seja iniciado. Você deverá ver um status semelhante à imagem a seguir:

   ![Informações de "histórico de compilação" com status do trabalho](./media/container-instances-jenkins/jenkins-job-status.png)

2. Enquanto o trabalho estiver em execução, abra o portal do Azure e examine o grupo de recursos Jenkins. Você deve ver que uma instância de contêiner foi criada. O trabalho Jenkins está sendo executado dentro desta instância.

   ![Instância de contêiner no grupo de recursos](./media/container-instances-jenkins/jenkins-aci.png)

3. Como Jenkins executa mais trabalhos do que o número configurado de executores de Jenkins (padrão 2), várias instâncias de contêiner são criadas.

   ![Instâncias de contêiner recém-criadas](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Depois que todos os trabalhos de compilação forem concluídos, as instâncias de contêiner serão removidas.

   ![Grupo de recursos com instâncias de contêiner removidas](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Resolver problemas nos plug-ins do Jenkins

Se se deparar com erros nos plug-ins do Jenkins, comunique os problemas com os componentes específicos no [Jenkins JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Jenkins no Azure, consulte [Azure e Jenkins][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-identities-azure-resources]: ../active-directory/managed-identities-azure-resources/overview.md