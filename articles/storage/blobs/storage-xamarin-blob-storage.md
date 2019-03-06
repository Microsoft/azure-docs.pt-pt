---
title: Como utilizar o armazenamento de (Blob objeto) do Xamarin | Documentos da Microsoft
description: A biblioteca de cliente de armazenamento do Azure para Xamarin permite aos programadores criar iOS, Android e Windows Store apps com suas interfaces de utilizador nativa. Este tutorial mostra como utilizar o Xamarin para criar uma aplicação que utiliza o armazenamento de Blobs do Azure.
services: storage
documentationcenter: xamarin
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: cac889b83bb0e7a7aab12ff4097556abf60663b6
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449126"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Como utilizar o armazenamento de Blobs do Xamarin

Base de código Xamarin permite que os desenvolvedores a usar um partilhado c# para criar o iOS, Android e Windows Store apps com suas interfaces de utilizador nativa. Este tutorial mostra-lhe como utilizar o armazenamento de Blobs do Azure com uma aplicação Xamarin. Se gostaria de saber mais sobre o armazenamento do Azure, antes de mergulhar no código, consulte [introdução ao armazenamento do Microsoft Azure](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Criar uma nova aplicação Xamarin
Para este tutorial, criaremos um aplicativo dirigido para Android, iOS e Windows. Esta aplicação será simplesmente criar um contentor e carregar um blob para este contentor. Vamos utilizar Visual Studio no Windows, mas as lições aprendidas mesmo podem ser aplicadas durante a criação de uma aplicação com o Xamarin Studio em macOS.

Siga estes passos para criar a sua aplicação:

1. Se ainda não o fez, transfira e instale [Xamarin para Visual Studio](https://www.xamarin.com/download).
2. Abra o Visual Studio e crie uma aplicação em branco (nativo portátil): **Ficheiro > novo > projeto > Multiplataforma > aplicação (nativo portátil) em branco**.
3. Sua solução no painel do Explorador de soluções com o botão direito e selecione **gerir pacotes NuGet para solução**. Procure **windowsazure. Storage** e instale a versão estável mais recente a todos os projetos na sua solução.
4. Crie e execute seu projeto.

Agora, deve ter um aplicativo que pode clicar num botão que incrementa um contador.

## <a name="create-container-and-upload-blob"></a>Criar contentor e carregar blob
Em seguida, em sua `(Portable)` projeto, irá adicionar algum código para `MyClass.cs`. Este código cria um contentor e carrega um blob para este contentor. `MyClass.cs` deve ter um aspeto semelhante ao seguinte:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

Certifique-se substituir "your_account_name_here" e "your_account_key_here" com o nome da conta real e a chave de conta. 

O iOS, Android e Windows Phone projetos todos se referem a seu projeto portátil – que significa que pode escrever todo o seu código compartilhado em um colocam e utilizá-lo em todos os seus projetos. Agora pode adicionar a seguinte linha de código para cada projeto para começar a aproveitar as vantagens: `MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc. that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Executar a aplicação
Agora, pode executar esta aplicação no emulador do Android ou Windows Phone. Também pode executar esta aplicação no emulador do iOS, mas isso exigirá um MAC. Para obter instruções específicas sobre como fazer isso, leia a documentação para [ligar o Visual Studio para Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Depois de executar a aplicação, irá criar o contentor `mycontainer` na sua conta de armazenamento. Esta deve conter o blob `myblob`, que tem o texto, `Hello, world!`. Pode verificar isto utilizando o [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/).

## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a criar uma aplicação de várias plataformas no Xamarin que utiliza o armazenamento do Azure, especificamente tendo num cenário no armazenamento de Blobs. No entanto, pode fazer muito mais com o armazenamento de BLOBs não só, mas também com a tabela, ficheiros e armazenamento de filas. Veja os artigos seguintes para obter mais informações:

* [Introdução ao armazenamento de Blobs do Azure através do .NET](storage-dotnet-how-to-use-blobs.md)
* [Introdução aos ficheiros do Azure](../files/storage-files-introduction.md)
* [Programar para os Ficheiros do Azure com .NET](../files/storage-dotnet-how-to-use-files.md)
* [Introdução ao armazenamento de Tabelas do Azure através do .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Introdução ao Armazenamento de filas do Azure através do .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]