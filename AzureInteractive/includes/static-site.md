O Armazenamento de Blobs do Azure é um serviço de baixo custo e altamente escalonável que pode ser usado para hospedar arquivos estáticos. Para este tutorial, você o usará para fornecer conteúdo estático (por exemplo, HTML, JavaScript, CSS) ao aplicativo Web compilado.

## <a name="create-a-storage-account"></a>Criar uma conta de Armazenamento

Conta de armazenamento é um recurso do Azure que permite armazenar tabelas, filas, arquivos, blobs (objetos) e discos de máquina virtual.

1. Faça logon no Cloud Shell (Bash), selecionando o botão **Entrar no modo de foco**. Esse botão está no canto superior direito ou no final da página, dependendo da amplitude da janela do navegador. O modo de foco encaixa uma janela do Cloud Shell no lado direito da janela do navegador e, portanto, você pode executar os comandos que são mostrados no tutorial facilmente.

1. No Azure, um grupo de recursos é um contêiner que mantém os recursos do Azure relacionados para facilitar o gerenciamento. Crie um novo grupo de recursos denominado **first-serverless-app**.

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. O conteúdo estático (arquivos HTML, CSS e JavaScript) para este tutorial é hospedado no Armazenamento de Blobs. O Armazenamento de Blobs exige uma conta de armazenamento. Crie uma conta de armazenamento (Uso geral V2) no grupo de recursos. Substitua `<storage account name>` por um nome exclusivo.

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. Usando a barra de pesquisa na parte superior do [portal do Azure](https://portal.azure.com), localize e abra a conta de armazenamento que você acabou de criar.

1. No painel de navegação esquerdo, selecione **Site estático (versão prévia)** a fim de configurar um contêiner para hospedagem de site estático.
    - Selecione **Habilitado** para habilitar o site estático.
    - Insira *index.html* como o nome do documento de índice. O campo já tem *index.html* em uma fonte cinza, mas isso é apenas o texto de exemplo; você ainda precisará inserir esse valor no campo.
    - Clique em **Salvar**.
    
    ![Inserir configurações do site estático](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. Salve o **Ponto de Extremidade Primário** em algum lugar conveniente de onde ele possa ser copiado durante o tutorial. Esta é a URL do aplicativo Web.

## <a name="upload-the-web-application"></a>Carregar o aplicativo Web

1. Os arquivos de origem do aplicativo compilado neste tutorial estão localizados em um [repositório do GitHub](https://github.com/Azure-Samples/functions-first-serverless-web-application). Verifique se você está em seu diretório base no Cloud Shell e clone esse repositório.

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    O repositório é clonado em `/home/<username>/functions-first-serverless-web-application`.

1. O aplicativo Web no lado do cliente está localizado na pasta **www** pasta e é criado com a estrutura JavaScript Vue.js. Alterne para a pasta e execute os comandos de npm para instalar as dependências e compilar o aplicativo. O último desses comandos pode levar vários minutos para ser concluído.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    O aplicativo é gerado na pasta **dist**.

1. Alterne o diretório atual para **dist** e carregue o aplicativo no contêiner de blobs **$web**.

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. Abra a URL do ponto de extremidade primário dos sites estáticos do Armazenamento em um navegador para exibir o aplicativo.

    ![Primeira home page do aplicativo Web sem servidor](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a>Resumo

Neste módulo, você criou um grupo de recursos denominado **first-serverless-app** que contém uma conta de armazenamento. Um contêiner de blobs denominado **$web** na conta de armazenamento armazena o conteúdo estático do aplicativo Web e disponibiliza o conteúdo publicamente. Em seguida, você aprenderá a usar uma função sem servidor para carregar imagens para o Armazenamento de Blobs a partir do aplicativo Web.
