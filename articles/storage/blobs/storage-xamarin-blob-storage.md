---
title: Como utilizar o armazenamento de objetos (Blob) de Xamarin [ Microsoft Docs
description: A biblioteca de clientes Azure Storage para xamarin permite aos desenvolvedores criar aplicações iOS, Android e Windows Store com as suas interfaces de utilizador nativo. Este tutorial mostra como usar xamarin para criar uma aplicação que utiliza o armazenamento Azure Blob.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 8a1c91c8a8a59af26386e70e68e7c4fd93f5eaa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726346"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Como usar o Blob Storage de Xamarin

O Xamarin permite que os desenvolvedores utilizem uma base de código sem partilhado C# para criar aplicações iOS, Android e Windows Store com as suas interfaces de utilizador nativo. Este tutorial mostra-lhe como usar o armazenamento Azure Blob com uma aplicação Xamarin. Se quiser saber mais sobre o Armazenamento Azure, antes de mergulhar no código, consulte introdução [ao Armazenamento Microsoft Azure](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Criar uma nova Aplicação Xamarin

Para este tutorial, vamos criar uma aplicação que visa Android, iOS e Windows. Esta aplicação irá simplesmente criar um recipiente e enviar uma bolha para este recipiente. Vamos usar o Visual Studio no Windows, mas as mesmas aprendizagens podem ser aplicadas ao criar uma aplicação usando o Xamarin Studio no macOS.

Siga estes passos para criar a sua aplicação:

1. Se ainda não o fez, faça o download e instale [o Xamarin para o Estúdio Visual.](https://www.xamarin.com/download)
2. Open Visual Studio, e criar uma app em branco (Portátil Nativo): **File > New > Project > Cross-Platform > App Em branco (Portátil Nativo)**.
3. Clique à direita na sua solução no painel do Solution Explorer e selecione **Gerir pacotes NuGet para solução**. Procure **windowsAzure.Storage** e instale a versão mais recente estável para todos os projetos da sua solução.
4. Construa e gereno seu projeto.

Deve agora ter uma aplicação que lhe permite clicar num botão que incrementa um contador.

## <a name="create-container-and-upload-blob"></a>Criar recipiente e carregar blob

Em seguida, `(Portable)` no âmbito do seu `MyClass.cs`projeto, vai adicionar algum código a . Este código cria um recipiente e envia uma bolha para este recipiente. `MyClass.cs`deve parecer o seguinte:

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

Certifique-se de substituir "your_account_name_here" e "your_account_key_here" pelo nome real da conta e chave de conta.

Os projetos iOS, Android e Windows Phone têm referências ao seu projeto portátil - o que significa que pode escrever todo o seu código partilhado num só local e usá-lo em todos os seus projetos. Pode agora adicionar a seguinte linha de código a cada projeto para começar a tirar partido:`MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Dróide > MainActivity.cs

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

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=391641

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

Agora pode executar esta aplicação num emulador Android ou Windows Phone. Também pode executar esta aplicação num emulador iOS, mas isso exigirá um Mac. Para obter instruções específicas sobre como fazê-lo, leia a documentação para ligar o [Estúdio Visual a um Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Assim que executar a sua aplicação, criará o recipiente `mycontainer` na sua conta de Armazenamento. Deve conter a bolha, `myblob`que tem `Hello, world!`o texto, . Pode verificar isto utilizando o [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar uma aplicação cross-platform em Xamarin que utiliza o Armazenamento Azure, focando-se especificamente num cenário no Blob Storage. No entanto, pode fazer muito mais com não só o Armazenamento blob, mas também com mesa, arquivo e armazenamento de fila. Por favor, confira os seguintes artigos para saber mais:

* [Introdução ao armazenamento de Blobs do Azure através do .NET](storage-dotnet-how-to-use-blobs.md)
* [Introdução aos Ficheiros do Azure](../files/storage-files-introduction.md)
* [Programar para os Ficheiros do Azure com .NET](../files/storage-dotnet-how-to-use-files.md)
* [Introdução ao armazenamento de Tabelas do Azure através do .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Começar com o armazenamento de fila Azure usando .NET](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
