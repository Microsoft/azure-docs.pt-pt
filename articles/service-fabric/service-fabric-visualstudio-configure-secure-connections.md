---
title: Configurar ligações de cluster de tecido de serviço Azure seguros
description: Aprenda a usar o Visual Studio para configurar ligações seguras que são suportadas pelo cluster Azure Service Fabric.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: d4d6b781d97d481793e69cf2ca97cca5b93ce432
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008536"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Configure ligações seguras a um cluster de tecido de serviço do Visual Studio
Aprenda a usar o Visual Studio para aceder de forma segura a um cluster de tecido de serviço Azure com políticas de controlo de acesso configuradas.

## <a name="cluster-connection-types"></a>Tipos de conexão de cluster
Dois tipos de ligações são suportados pelo cluster Azure Service Fabric: ligações **não seguras** e ligações seguras **baseadas em certificados x509.** (Para os clusters de tecido de serviço alojados no local, as autenticações **do Windows** e **do dSTS** também são suportadas.) Tem de configurar o tipo de ligação de cluster quando o cluster está a ser criado. Uma vez criado, o tipo de ligação não pode ser alterado.

As ferramentas Visual Studio Service Fabric suportam todos os tipos de autenticação para a ligação a um cluster para publicação. Consulte [a configuração de um cluster de tecido de serviço a partir do portal Azure](service-fabric-cluster-creation-via-portal.md) para obter instruções sobre como configurar um cluster de tecido de serviço seguro.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Configure as ligações de cluster em perfis de publicação
Se publicar um projeto de Tecido de Serviço da Visual Studio, utilize a caixa de diálogo **de aplicação de tecido de serviço de publicação** para escolher um cluster de tecido de serviço Azure. No **ponto final de Ligação,** selecione um cluster existente sob a sua subscrição.

![A caixa de diálogo **Publish Service Fabric Application** é utilizada para configurar uma ligação de tecido de serviço.][publishdialog]

A caixa de diálogo **de aplicação de tecido de serviço** de publicação valida automaticamente a ligação do cluster. Se solicitado, inscreva-se na sua conta Azure. Se a validação passar, significa que o seu sistema tem os certificados corretos instalados para ligar ao cluster de forma segura, ou o seu cluster não é seguro. Falhas de validação podem ser causadas por problemas de rede ou por não ter o seu sistema corretamente configurado para se ligar a um cluster seguro.

![A caixa de diálogo **Publish Service Fabric Application** valida uma conexão de cluster de tecido de serviço existente e corretamente configurada.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Para ligar a um cluster seguro
1. Certifique-se de que pode aceder a um dos certificados de cliente que o cluster de destino confia. O certificado é geralmente partilhado como um ficheiro Personal Information Exchange (.pfx). Consulte [a criação de um cluster de Tecidos de Serviço a partir do portal Azure](service-fabric-cluster-creation-via-portal.md) para saber como configurar o servidor para conceder acesso a um cliente.
2. Instale o certificado de confiança. Para isso, clique duas vezes no ficheiro .pfx ou utilize o script PowerShell Import-PfxCertificate para importar os certificados. Instale o certificado em **Cert:\LocalMachine\My**. Não há problema em aceitar todas as definições predefinidos enquanto importa o certificado.
3. Escolha o comando **Publicar...** no menu de atalho do projeto para abrir a caixa de diálogo **de aplicação Publicar Azure** e, em seguida, selecione o cluster alvo. A ferramenta resolve automaticamente a ligação e guarda os parâmetros de ligação seguros no perfil de publicação.
4. Opcional: Pode editar o perfil de publicação para especificar uma ligação de cluster segura.
   
   Uma vez que está a editar manualmente o ficheiro Publish Profile XML para especificar as informações do certificado, certifique-se de que regista o nome da loja de certificados, a localização da loja e a impressão digital do certificado. Você precisará fornecer estes valores para o nome da loja do certificado e localização da loja. Ver [Como: Recuperar a impressão digital de um certificado](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) para mais informações.
   
   Pode utilizar os parâmetros *ClusterConnectionParameters* para especificar os parâmetros PowerShell a utilizar ao ligar-se ao cluster de Tecido de Serviço. Os parâmetros válidos são os que são aceites pelo Connect-ServiceFabricCluster cmdlet. Consulte [o Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para obter uma lista de parâmetros disponíveis.
   
   Se estiver a publicar num cluster remoto, tem de especificar os parâmetros apropriados para esse cluster específico. Segue-se um exemplo de ligação a um cluster não seguro:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Aqui está um exemplo para a ligação a um cluster seguro baseado em certificado x509:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Editar quaisquer outras definições necessárias, tais como parâmetros de atualização e localização do ficheiro Application Parameter, e publicar a sua aplicação a partir da caixa de diálogo **de aplicação de tecido de serviço de publicação** no Estúdio Visual.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o acesso a clusters de Tecidos de Serviço, consulte [visualizar o seu cluster utilizando o Service Fabric Explorer.](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
