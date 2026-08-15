Step-by-Step Production Launch Runbook
When you are ready to launch this entire setup on your live production server, follow this clean execution sequence to bring up your infrastructure:
bash
# Step 1: Clone your updated repository files context cleanly onto the host server
git clone https://github.com
cd janavani-website

# Step 2: Initialize environment validation rules and toolchains
chmod +x setup_dev.sh
./setup_dev.sh

# Step 3: Fill in your live server configurations inside the production environment file
# Enter valid internal values for SMTP mail relays and secure domains
nano .env

# Step 4: Execute the unified production rolling build and deployment pipeline script
chmod +x deploy_production.sh
./deploy_production.sh
Use code with caution.
________________________________________

