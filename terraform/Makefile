# Makefile to deploy, test, and clean Lambda Function
REGION = eu-central-1
FUNCTION_NAME = otel-lambda-test1

# Deploy Lambda Function
deploy:
	@echo "Deploying Lambda function..."
	terraform init
	@echo "Refreshing Terraform state to pick up latest external data..."
	terraform plan -refresh-only
	@echo "Generating full deployment plan..."
	terraform plan
	@echo "Applying deployment plan..."
	terraform apply -auto-approve

# Test Lambda Function
test:
	@echo "Invoking Lambda function..."
	aws lambda invoke \
        --function-name $(FUNCTION_NAME) \
        --region $(REGION) \
        output.json
	@cat output.json

# Clean up Lambda Function and Terraform state
clean:
	@echo "Cleaning up..."
	rm -rf builds testing
	pwd
	terraform destroy -auto-approve