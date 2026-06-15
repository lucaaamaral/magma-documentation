# Copyright 2025 The Magma Authors.
# This source code is licensed under the BSD-style license found in the
# LICENSE file in the root directory of this source tree.
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#==================================================================
# Dockerfile based on Docusaurus community guide found at
# https://docusaurus.community/knowledge/deployment/docker/
#==================================================================

#==================================================================
# Base Node image
#==================================================================
# The slim version is based on a small Debian image.
FROM node:26-slim AS base
ENV FORCE_COLOR=0
# Bootstrap Yarn Classic (corepack is unbundled from Node 25+); yarn manages all project deps.
RUN npm install -g yarn@1.22.22
WORKDIR /opt/docusaurus

# Documentation contents from readmes/ (via additional_contexts)
COPY --from=readmes . docs
COPY src src
COPY static static
COPY versioned_docs versioned_docs
COPY versioned_sidebars versioned_sidebars

# Configuration files
COPY docusaurus.config.js .
COPY package.json yarn.lock ./
COPY sidebars.js .
COPY sidebars.json .
COPY test.js .
COPY versions.json .

WORKDIR /opt/docusaurus
RUN [ -d "node_modules" ] || yarn install --frozen-lockfile

#==================================================================
# Local deployment
#==================================================================
FROM base AS dev
EXPOSE 3000
CMD ["yarn", "start", "--poll", "1000"]

#==================================================================
# Base image for production deployment
#==================================================================
FROM base AS prod
ENV NODE_OPTIONS="--max-old-space-size=4096"
RUN yarn build

#==================================================================
# Production deployment
#==================================================================
FROM prod AS serve
EXPOSE 3000
CMD ["yarn", "serve", "--no-open"]

#==================================================================
# Production deployment with Caddy automatic SSL/TLS
#==================================================================
FROM caddy:2-alpine AS caddy
COPY --from=prod /opt/docusaurus/Caddyfile /etc/caddy/Caddyfile
COPY --from=prod /opt/docusaurus/build /var/docusaurus