### Step 1: Create GitHub Actions Workflow

- Create a ```.github/workflows``` directory in your repository root
- Create a new file named ```deploy.yml``` in the workflows directory
- Add the following workflow configuration:

```yaml
name: 🚀 Deploy website on Staging/Demo Server
on: [push]
jobs:
  web-deploy:
    if: github.ref == 'refs/heads/develop'
    name: 🎉 Deploy on Development Branch
    runs-on: ubuntu-latest
    steps:
      - name: 🚚 Get latest code
        uses: actions/checkout@v2

      - name: 🚚 Executing Git Pull on Server.
        uses: SamKirkland/FTP-Deploy-Action@4.3.0
        with:
          command: |
            cd public_html/
            chmod -R 775 storage
            chmod -R 775 bootstrap/cache
            php artisan storage:link
            php artisan optimize
            php artisan migrate

          server: ${{ secrets.server_host }}
          port: ${{ secrets.port }}
          username: ${{ secrets.ftp_username }}
          password: ${{ secrets.ftp_password }}
          server-dir: /public_html/
```

### Step 2: Configure Repository Secrets

- Go to your GitHub repository settings
- Navigate to **Secrets and variables** → **Actions**
- Add the following secrets:
  - ```server_host```: Your server hostname or IP address
  - ```port```: Server port (usually 21 for FTP or 22 for SFTP)
  - ```ftp_username```: Your FTP/SFTP username
  - ```ftp_password```: Your FTP/SFTP password

### Step 3: Environment Configuration

- Ensure your ```.env``` file is properly configured on the server with:
  ```bash
    DB_CONNECTION=mysql
    DB_HOST=your_database_host
    DB_PORT=3306
    DB_DATABASE=your_database_name
    DB_USERNAME=your_database_username
    DB_PASSWORD=your_database_password
  ```

### Step 4: Deployment Trigger

- Push your code to the ```develop``` branch to trigger automatic deployment
- The workflow will automatically:
  - Checkout the latest code
  - Deploy to your server
  - Set proper permissions for storage and cache directories
  - Create storage symlink
  - Optimize the application
  - Run database migrations
