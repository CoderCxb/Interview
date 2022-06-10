```ts
import * as path from 'path';
import * as webpack from 'webpack';
import 'webpack-dev-server';
import { parse } from 'yaml';

const HtmlWebpackPlugin = require('html-webpack-plugin');
const CssMinimizerPlugin = require("css-minimizer-webpack-plugin");
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

const TerserPlugin = require("terser-webpack-plugin");
const HardSourceWebpackPlugin = require('hard-source-webpack-plugin');
// const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;

const MiniTerserPlugin = require('./src/plugins/MiniTerser');

// --env设置env上的属性
// --node-env 设置process.env.NODE_ENV的值
const configuration = function (env: any, argv:any): webpack.Configuration {
  // 注意: 所有赋值给process.env作为属性的值都会被转换成字符串
  // 此处webpack命令需要添加 --node-env production
  const isProduction = process.env.NODE_ENV === 'production';
  return {
    // 绝对路径, 当前文件的上下文环境, 后续配置都是相对这个路径
    context : path.resolve(__dirname, '../'),
    entry: {
      index: {
        import: './src/index.js',
        // dependOn: 'lodash'
      },
      max: './src/math.js',
      // lodash: 'lodash'
    },
    externals: {
      lodash: '_'
    },
    cache: {
      type: 'memory'
    },
    // cache: false,
    // mode: 'development',
    mode: 'production',
    // devtool: 'cheap-module-source-map',
    devtool: "source-map", 
    output: {
      // 入口文件打包的名字
      filename: '[name].[chunkhash].bundle.js',
      // 打包的chunk对应的文件名 [name]可以被魔法注释修改
      // 魔法注释: import(/* webpackChunkName: "npkName": true */ 'npk')
      chunkFilename: '[name].chunk.js',
      // path: path.resolve(__dirname, 'dist'),
      clean: true, // 每次打包是否清空
      assetModuleFilename: '[contenthash].asset[ext]',
      // publicPath: './dist', // 生产环境指定配置公共路径
    },
    devServer: {
      static: '/', 
      hot: true,
      // host: '192.168.1.102'
    },
    resolve: {
      alias: {
        'Profile': path.resolve(__dirname, 'src/profile/'),
      },
    },
    plugins: [
      // new HardSourceWebpackPlugin(),
      new HtmlWebpackPlugin({
        template: path.resolve(__dirname, './index.html'),
        cache: true, // 当文件没有发生改变时,使用缓存
        minify: false, // html是否进行压缩
      }),
      new MiniCssExtractPlugin({
        filename: '[name].[contenthash].css'
      }),
      new webpack.DefinePlugin({
        __VUE_OPTIONS_API__: JSON.stringify(false),
        // 如果变量作为字符串时,如果解析错误,就改成这样
        __BASE_URL__: '"./"'
      }),
      // new webpack.DllPlugin({
      // }),
      // new BundleAnalyzerPlugin(),
      // new MiniTerserPlugin({
      //   title: 'Mini Terser Plugin',
      // })
    ],
    module: {
      rules: [
        {
          test: /\.m?jsx?$/,
          exclude: /(node_modules|bower_components)/,
          use: {
            loader: 'babel-loader',
            // options: {
            //   presets: ['@babel/preset-env', '@babel/preset-react'],
            //   // plugins: [ '@babel/plugin-transform-runtime']
            // }
          }
        },
        {
          test: /\.vue$/i,
          use: ['vue-loader']
        },
        {
          test: /\.css$/i,
          use: [
            // 生产环境打包成单独文件
            // 开发环境不拆分
            isProduction 
              ? MiniCssExtractPlugin.loader 
              : 'style-loader',
            "css-loader",
            {
              loader: "postcss-loader",
              options: {
                postcssOptions: {
                  plugins: [
                    require('autoprefixer'),
                    require('postcss-preset-env')
                  ]
                }
              }
            }
          ],
        },
        {
          test: /\.(png|jpe?g|gif|svg)$/,
          type: 'asset/resource',
          generator: {
            filename: 'asset/image/[hash][ext]', // 优先级高于output中配置的assetModuleFilename
          } 
        },
        {
          test: /\.yaml/,
          type: 'asset/resource',
          parser: {
            parse
          },
          generator: {
            filename: 'asset/other/[hash][ext]'
          }
        },
      ],
    },
    optimization: {
      minimize: false,
      // usedExports: true,
      // 自定义minimizer时,原本的优化就无效了
      // minimizer: [
        // new CssMinimizerPlugin(),
        // new TerserPlugin()
      // ],
      moduleIds: 'deterministic', // moduleId改变可能会影响缓存, 将其设置为不可逆转, 确保第三方包打包后的hash不变
      runtimeChunk: 'single',
      splitChunks: {
        cacheGroups: {
          vendor: {
            test: /[\\/]node_modules[\\/]/,
            name: 'vendors',
            chunks: 'all',
          },
        },
      },
    }
  }
}

export default configuration
```