---
id: version-0.26-testing
title: testing
original_id: testing
---
<a id="content"></a><table width="100%"><tbody><tr><td><h1><a class="anchor" name="testing"></a>Testing <a class="hash-link" href="docs/testing.html#testing">#</a></h1></td><td style="text-align:right;"><a target="_blank" href="https://github.com/facebook/react-native/blob/master/docs/Testing.md">Edit on GitHub</a></td></tr></tbody></table><div><h2><a class="anchor" name="running-tests-and-contributing"></a>Running Tests and Contributing <a class="hash-link" href="docs/testing.html#running-tests-and-contributing">#</a></h2><p>The React Native repo has several tests you can run to verify you haven't caused a regression with your PR.  These tests are run with the <a href="http://docs.travis-ci.com/" target="_blank">Travis</a> continuous integration system, and will automatically post the results to your PR.</p><p>We don't have perfect test coverage of course, especially for complex end-to-end interactions with the user, so many changes will still require significant manual verification, but we would love it if you want to help us increase our test coverage and add more tests and test cases!</p><h2><a class="anchor" name="jest-tests"></a>Jest Tests <a class="hash-link" href="docs/testing.html#jest-tests">#</a></h2><p><a href="http://facebook.github.io/jest/" target="_blank">Jest</a> tests are JS-only tests run on the command line with node.  The tests themselves live in the <code>__tests__</code> directories of the files they test, and there is a large emphasis on aggressively mocking out functionality that is not under test for failure isolation and maximum speed.  You can run the existing React Native jest tests with</p><div class="prism language-javascript">npm test</div><p>from the react-native root, and we encourage you to add your own tests for any components you want to contribute to.  See <a href="https://github.com/facebook/react-native/blob/master/Examples/Movies/__tests__/getImageSource-test.js" target="_blank"><code>getImageSource-test.js</code></a> for a basic example.</p><p>Note: In order to run your own tests, you will have to first follow the Getting Started instructions on the Jest page and then include the <code>jest</code> objects below in <code>package.json</code> so that the scripts are pre-processed before execution.</p><div class="prism language-javascript"><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token string">"scripts"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
  <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
  <span class="token string">"test"</span><span class="token punctuation">:</span> <span class="token string">"jest"</span>
<span class="token punctuation">}</span><span class="token punctuation">,</span>
<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token string">"jest"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
  <span class="token string">"scriptPreprocessor"</span><span class="token punctuation">:</span> <span class="token string">"node_modules/react-native/jestSupport/preprocessor.js"</span><span class="token punctuation">,</span>
  <span class="token string">"setupEnvScriptFile"</span><span class="token punctuation">:</span> <span class="token string">"node_modules/react-native/jestSupport/env.js"</span><span class="token punctuation">,</span>
  <span class="token string">"testPathIgnorePatterns"</span><span class="token punctuation">:</span> <span class="token punctuation">[</span>
    <span class="token string">"/node_modules/"</span><span class="token punctuation">,</span>
    <span class="token string">"packager/react-packager/src/Activity/"</span>
  <span class="token punctuation">]</span><span class="token punctuation">,</span>
  <span class="token string">"testFileExtensions"</span><span class="token punctuation">:</span> <span class="token punctuation">[</span>
    <span class="token string">"js"</span>
  <span class="token punctuation">]</span><span class="token punctuation">,</span>
  <span class="token string">"unmockedModulePathPatterns"</span><span class="token punctuation">:</span> <span class="token punctuation">[</span>
    <span class="token string">"promise"</span><span class="token punctuation">,</span>
    <span class="token string">"source-map"</span>
  <span class="token punctuation">]</span>
<span class="token punctuation">}</span><span class="token punctuation">,</span>
<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span></div><p>Note: you may have to install/upgrade/link Node.js and other parts of your environment in order for the tests to run correctly.  Check out the latest setup in <a href="https://github.com/facebook/react-native/blob/master/.travis.yml#L11-24" target="_blank">.travis.yml</a></p><h2><a class="anchor" name="integration-tests-ios-only"></a>Integration Tests (iOS only) <a class="hash-link" href="docs/testing.html#integration-tests-ios-only">#</a></h2><p>React Native provides facilities to make it easier to test integrated components that require both native and JS components to communicate across the bridge.  The two main components are <code>RCTTestRunner</code> and <code>RCTTestModule</code>.  <code>RCTTestRunner</code> sets up the ReactNative environment and provides facilities to run the tests as <code>XCTestCase</code>s in Xcode (<code>runTest:module</code> is the simplest method).  <code>RCTTestModule</code> is exported to JS as <code>NativeModules.TestModule</code>.  The tests themselves are written in JS, and must call <code>TestModule.markTestCompleted()</code> when they are done, otherwise the test will timeout and fail.  Test failures are primarily indicated by throwing a JS exception.  It is also possible to test error conditions with <code>runTest:module:initialProps:expectErrorRegex:</code> or <code>runTest:module:initialProps:expectErrorBlock:</code> which will expect an error to be thrown and verify the error matches the provided criteria.  See <a href="https://github.com/facebook/react-native/blob/master/IntegrationTests/IntegrationTestHarnessTest.js" target="_blank"><code>IntegrationTestHarnessTest.js</code></a>, <a href="https://github.com/facebook/react-native/blob/master/Examples/UIExplorer/UIExplorerIntegrationTests/UIExplorerIntegrationTests.m" target="_blank"><code>UIExplorerIntegrationTests.m</code></a>, and <a href="https://github.com/facebook/react-native/blob/master/IntegrationTests/IntegrationTestsApp.js" target="_blank">IntegrationTestsApp.js</a> for example usage and integration points.</p><p>You can run integration tests locally with cmd+U in the IntegrationTest and UIExplorer apps in Xcode.</p><h2><a class="anchor" name="snapshot-tests-ios-only"></a>Snapshot Tests (iOS only) <a class="hash-link" href="docs/testing.html#snapshot-tests-ios-only">#</a></h2><p>A common type of integration test is the snapshot test.  These tests render a component, and verify snapshots of the screen against reference images using <code>TestModule.verifySnapshot()</code>, using the <a href="https://github.com/facebook/ios-snapshot-test-case" target="_blank"><code>FBSnapshotTestCase</code></a> library behind the scenes.  Reference images are recorded by setting <code>recordMode = YES</code> on the <code>RCTTestRunner</code>, then running the tests.  Snapshots will differ slightly between 32 and 64 bit, and various OS versions, so it's recommended that you enforce tests are run with the correct configuration.  It's also highly recommended that all network data be mocked out, along with other potentially troublesome dependencies.  See <a href="https://github.com/facebook/react-native/blob/master/IntegrationTests/SimpleSnapshotTest.js" target="_blank"><code>SimpleSnapshotTest</code></a> for a basic example.</p><p>If you make a change that affects a snapshot test in a PR, such as adding a new example case to one of the examples that is snapshotted, you'll need to re-record the snapshot reference image.  To do this, simply change to <code>_runner.recordMode = YES;</code> in <a href="https://github.com/facebook/react-native/blob/master/Examples/UIExplorer/UIExplorerIntegrationTests/UIExplorerSnapshotTests.m#L42" target="_blank">UIExplorer/UIExplorerSnapshotTests.m</a>, re-run the failing tests, then flip record back to <code>NO</code> and submit/update your PR and wait to see if the Travis build passes.</p></div><div class="docs-prevnext"><a class="docs-next" href="docs/javascript-environment.html#content">Next →</a></div><div class="survey"><div class="survey-image"></div><p>We are planning improvements to the React Native documentation. Your responses to this short survey will go a long way in helping us provide valuable content. Thank you!</p><center><a class="button" href="https://www.facebook.com/survey?oid=681969738611332">Take Survey</a></center></div>