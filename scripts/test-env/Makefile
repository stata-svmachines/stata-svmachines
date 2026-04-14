.PHONY: container
container: Containerfile Stata15Linux64.tar.gz
	podman build \
		--build-arg STATA_INSTALLER=Stata15Linux64.tar.gz \
		-f $< -t stata .

.PHONY: container-publish
container-publish: container
	@test -n "$(GITHUB_OWNER)" || (echo "Error: GITHUB_OWNER not defined" && false)
	@test -n "$(GITHUB_TOKEN)" || (echo "Error: GITHUB_TOKEN not defined" && false)
	podman tag stata:latest ghcr.io/$(GITHUB_OWNER)/stata:15
	echo $(GITHUB_TOKEN) | podman login ghcr.io -u $(GITHUB_OWNER) --password-stdin
	podman push ghcr.io/$(GITHUB_OWNER)/stata:15

Stata15Linux64.tar.gz:
	@echo "Download Stata15Linux64.tar.gz using a paid account at https://download.stata.com."
