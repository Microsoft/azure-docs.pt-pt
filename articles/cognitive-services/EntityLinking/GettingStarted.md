---
title: 'Tutorial: Criar uma aplicação de ligação de entidade-C#'
titlesuffix: Azure Cognitive Services
description: Analise texto e associe entidades com nome a entradas relevantes numa base de dados de conhecimento ao utilizar a API de Associação de Entidades.
services: cognitive-services
author: DavidLiCIG
manager: nitinme
ms.service: cognitive-services
ms.subservice: entity-linking-intelligence
ms.topic: tutorial
ms.date: 07/06/2016
ms.author: davl
ms.openlocfilehash: fc1bdd5c6ad4829e22af9922c6749e60f842abaf
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594037"
---
# <a name="tutorial-build-an-entity-linking-app-with-c"></a>Tutorial: Criar uma aplicação de ligação da entidade comC#

A Associação de Entidades da Microsoft é uma ferramenta de processamento de linguagem natural para analisar texto e associar entidades com nome a entradas relevantes numa base de dados de conhecimento. 

Este tutorial explora a associação de entidades ao utilizar a Biblioteca de Cliente de Associação de Entidades como um pacote NuGet. 

### <a name="Prerequisites">Pré-requisitos</a>

- Visual Studio 2015
- Uma Chave da API de Serviços Cognitivos da Microsoft
- Obter a biblioteca de cliente e o exemplo
- Pacote NuGet de Associação de Entidades da Microsoft

Pode transferir a Biblioteca de Cliente da API do Serviço de Inteligência de Associação de Entidades através do [SDK](https://www.github.com/microsoft/cognitive-entitylinking-windows). O ficheiro zip transferido tem de ser extraído para uma pasta à sua escolha, vários utilizadores escolhem a pasta do Visual Studio 2015.

### <a name="step-1-subscribe-entity-linking-intelligence-service-and-get-your-own-key">Passo 1: Subscrever Entity Linking Intelligence Service e obtenha a chave</a>
Antes de utilizar o Serviço de Inteligência de Associação de Entidades, tem de se inscrever para uma chave de API. Veja [Subscrições](https://www.microsoft.com/cognitive-services/en-us/sign-up). Tanto a chave primária como a secundária podem ser utilizadas neste tutorial.

### <a name="step-2-create-a-new-project-in-visual-studio"> Passo 2: Criar um novo projeto no Visual Studio</a>

Comecemos por criar um novo projeto no Visual Studio. Em primeiro lugar, inicie o Visual Studio 2015 no Menu Iniciar. Em seguida, crie um novo projeto ao selecionar **Installed → Templates → Visual C# → Windows Universal → Blank App** (Instalado → Modelos → Visual C# → Universal do Windows → Aplicação em Branco) para o modelo do seu projeto:

 ![Criar uma aplicação universal](./Images/CreateUWP.png)

### <a name="step-3-add-the-entity-linking-nuget-package-to-your-project">Passo 3: Adicionar o pacote de NuGet do Entity Linking ao seu projeto</a>

A Associação de Entidades dos Serviços Cognitivos é disponibilizada como um pacote NuGet.org e tem de ser instalada antes de a poder utilizar.
Para a adicionar ao seu projeto, aceda ao separador **Explorador de Soluções**, clique com o botão direito do rato no projeto e selecione **Gerir Pacotes Nuget**.

Em primeiro lugar, na janela **Gestor de Pacotes NuGet**, selecione NuGet.org como a sua **Origem do Pacote** no canto superior direito. Selecione **Procurar** no canto superior esquerdo e, na caixa de pesquisa, escreva "ProjectOxford.EntityLinking". Selecione o pacote NuGet **Microsoft.ProjectOxford.EntityLinking** e clique em **Instalar**.

Em seguida, procure o Newtonsoft.Json e instale-o. Se lhe for pedido para rever as alterações, clique em **OK**. Se forem apresentados os termos de licenciamento da Associação de Entidades, clique em **Aceito**.

A Associação de Entidades está agora instalada como parte da sua aplicação. Pode confirmar a instalação ao verificar se a referência ** Microsoft.ProjectOxford.EntityLinking** é apresentada como parte do seu projeto no Explorador de Soluções.

 ![Biblioteca de NuGet incluída no projeto](./Images/NugetLibraryInProject.png)
 
### <a name="step-4-add-an-input-and-output-text-block-to-your-apps-xaml">Passo 4: Adicionar um bloco de texto de entrada e saída para o XAML da sua aplicação</a>
Navegue para **mainpage. XAML** na **Explorador de soluções**, em seguida, faça duplo clique em arquivo que será aberto numa nova janela. Para sua conveniência, pode fazer duplo clique no botão **XAML**, no separador **Estruturador**, para ocultar o **Estruturador Visual** e reservar todo o espaço para a vista de código.

 ![Biblioteca de NuGet incluída no projeto](./Images/UWPMainPage.png)
 
A melhor forma de visualizar as funcionalidades num serviço de texto é criar um bloco de texto de entrada e de saída. Para o fazer, adicione o seguinte código XAML à **Grelha**. Este código adiciona três componentes: uma caixa de texto de entrada, um bloco de texto de saída e um botão de início.
 
 ```XAML
 <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="*"/>
        <RowDefinition Height="*" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <TextBox x:Name="inputBox" Grid.Row="0" TextWrapping="Wrap" Text="Enter a paragraph" Margin="10" AcceptsReturn="True" />
    <TextBlock x:Name="outputBlock" Grid.Row="1" TextWrapping="Wrap" Text="Result will be here" Margin="10" />
    <Button x:Name="button" Grid.Row="2" Content="Get Result" />
</Grid>
 ```
 
### <a name="step-5-proceed-to-add-entity-linking-intelligence-service">Passo 5: Adicionar Entity Linking Intelligence Service</a>
 
A interface de utilizador foi criada. Antes de utilizar o Serviço de Associação de Entidades, é necessário adicionar o processador de clique no botão. Abra o ficheiro **MainPage.xaml** no **Explorador de Soluções**. Adicione o processador button_Click ao fim do botão.
 
 ```XAML
 <Button x:Name="button" Grid.Row="2" Content="Get Result" Click="button_Click" />
 ```
 
É necessário implementar um processador de clique no botão no código. Abra o ficheiro **MainPage.xaml.css** no **Explorador de Soluções** para implementar o clique no botão. O EntityLinkingServiceClient é um wrapper para obter respostas da Associação de Entidades. O argumento de construtor de EntityLinkingServiceClient é a chave de subscrição dos Serviços Cognitivos. Cole a chave de subscrição que obteve no **Passo 1** para chamar o Serviço de Associação de Entidades. 

Segue-se um código de exemplo que adiciona "wikipediaId" à resposta ao utilizar o Serviço de Associação de Entidades. 
 
 ```csharp
 private async void button_Click(object sender, RoutedEventArgs e)
{
    var text = this.inputBox.Text;
    var client = new EntityLinkingServiceClient("Your subscription key","https://api.labs.cognitive.microsoft.com");
    var linkResponse = await client.LinkAsync(text);
    var result = string.Join(", ", linkResponse.Select(i => i.WikipediaID).ToList());
    this.outputBlock.Text = result;
}
 ```
 
Agora está pronto para executar a sua primeira Aplicação de Associação de Entidades para processamento de linguagem natural. Prima a **tecla F5** para compilar e iniciar a aplicação. Cole fragmentos de texto ou parágrafos na caixa de entrada. Prima o botão "Obter Resultado" e observe as entidades identificadas no bloco de saída.
 
 ![Resultado do Exemplo de Aplicação UWP](./Images/DemoCodeResult.png)
 
### <a name="summary">Resumo</a>
 
Neste tutorial, ficou a saber como criar uma aplicação para tirar partido da Biblioteca de Cliente do Serviço de Inteligência de Associação de Entidades com apenas algumas linhas de código C# e XAML. 

